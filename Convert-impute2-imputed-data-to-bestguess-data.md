Assuming you have used [impute2](https://mathgen.stats.ox.ac.uk/impute/impute_v2.html) to perform the imputation and generated `bgen` dosage files, you can use [qctool](http://www.well.ox.ac.uk/~gav/qctool/#overview) to filter on MAF and info and prepare a SNP quality file for the pipeline following the bash script below. Both plink1.90 and plink2 have been implemented in the pipeline.
You may have the following files:\
`
data_chr1.bgen, ... data_chr22.bgen;
data_chr1.bgi,  ... data_chr22.bgi;
data.sample
`\
They are all stored in `$genodir=Path_To_Your_Genotype_Data`.
```bash
cd $genodir
```
# Step 1: Create your filtered sample list
```bash
# If you need to filter out samples, generate an inclusion/exclusion list.
# Both exclusion and inclusion filtering are possible. This example demonstrates inclusion filtering.
qctool -g data_chr22.bgen -s data.sample -incl-samples inclusion.sample.txt -os filtered.sample
```
# Step 2: Generate bfiles and info files by each chromosome.
```
#!/bin/bash
# Please change {1..23} if you have chr 23.
for i in {1..22}; do
    echo "Processing chromosome ${i} ..."

    qctool -g filtered_R203_chr${i}.bgen -s filtered_R203.sample -og filteredchr${i}.bgen -incl-samples $sas_mother
    qctool -g filteredchr${i}.bgen -snp-stats -osnp data_chr${i}.snp-stats

    # Assume that column 14 is MAF and column 17 is info
    awk '$14>=0.01 && $17>=0.8 {print $2,$14,$17}' <data_chr${i}.snp-stats > data_chr${i}.info

    # Change format from chr:pos:A1:A2 to chr:pos_A1_A2
    awk 'NR==1 {print; next}
         $0 !~ /^#/ && NF {
           split($1,f,":");
           chr=f[1]; pos=f[2]; a1=f[3]; a2=f[4];
           if (a1 > a2) { t=a1; a1=a2; a2=t }
           id = chr ":" pos "_" a1 "_" a2;
           print id, $2, $3
         }' data_chr${i}.info > data_chr${i}.info.tmp && mv data_chr${i}.info.tmp data_chr${i}.info

    awk '{print $1}' <data_chr${i}.info > high_quality_SNPs_chr${i}.txt

    plink2 --bgen filteredchr${i}.bgen ref-first --sample filtered.sample --set-all-var-ids @:#_\$1_\$2 --new-id-max-allele-len 70 --make-bed --out data_chr${i}_filtered --hard-call-threshold 0.499999 --extract high_quality_SNPs_chr${i}.txt

    # For simplicity remove any duplicates in bim
    cp data_chr${i}_filtered.bim data_chr${i}_filtered.bim.orig
    awk '{
            if (++dup[$2] > 1) {
                print $1, $2".duplicate."dup[$2], $3, $4, $5, $6
            } else {
                print $0 }
        }' data_chr${i}_filtered.bim.orig > data_chr${i}_filtered.bim
    grep "duplicate" data_chr${i}_filtered.bim | awk '{ print $2 }' > duplicates.chr${i}.txt
    # update bfiles
    plink2 --bfile data_chr${i}_filtered --exclude duplicates.chr${i}.txt --new-id-max-allele-len 70 --make-bed --out data_chr${i}_filtered_nodup

    # remove duplicates in info file
    cp data_chr${i}.info data_chr${i}.info.orig
    awk '{
            if (++dup[$1] > 1) {
                print $1".duplicate."dup[$1], $2, $3
            } else {
                print $0 }
    }' data_chr${i}.info.orig > data_chr${i}.info
    fgrep -v -w -f duplicates.chr${i}.txt data_chr${i}.info > chr${i}_filtered.info
done
```
# Step 3: Merge them into one dataset
```
# Please adjust {2..23} if you have chromosome 23.
for i in {2..22}
do 
    echo "data_chr${i}_filtered_nodup"
done > mergefile.txt

plink1.90 --bfile data_chr1_filtered_nodup --new-id-max-allele-len 70 --merge-list mergefile.txt --make-bed --out data_filtered

# Combine info files into a single file

head -n1 chr01_filtered.info > data_filtered.info

for i in {1..22}
do
    awk ' NR>1 {print $0}' < chr${i}_filtered.info |cat >> data_filtered.info
done

```


The result should be three plink files: `data_filtered.bed`, `data_filtered.bim`, `data_filtered.fam`, and the info file: `data_filtered.info`. Copy them to `DEEP_mqtls/input_data` and set the following variable in your `config` file:

    bfile_raw="${home_directory}/input_data/data_filtered"

and

    quality_scores="${home_directory}/input_data/data_filtered.info"
    quality_type="impute2"