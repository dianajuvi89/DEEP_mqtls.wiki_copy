
```bash
# This exclusion list has one column with "IID" as header
exclindiv="all_individuals_to_exclude.txt"


for i in {1..23}
do
# If you need to convert your data, don't use any other flags besides `--out` the inclusion of `dosage=HDS` will introduce missingness and cause issues later on in the pipeline
${plink2} --vcf data_chr${i}.dose.vcf.gz --make-pgen --out data_chr${i}_dose --keep-allele-order

# You need to make sure the prefix of `pfile` is different from `--out`
# Rename the SNP IDs if necessary to avoid possible duplicates. `set-all-var-ids` will recode your variants to chr:pos:allele1_allele2 where # allele 1 and 2 are ascii sorted
${plink2} --pfile data_chr${i}_dose --remove $exclindiv --make-pgen --out data_chr${i}_indiv_filtered --set-all-var-ids @:#_\$1_\$2 --keep-allele-order
cp data_chr${i}_indiv_filtered.pvar data_chr${i}.info

# Keep SNPs with MAF>0.01 or info>0.8
${plink2} --pfile data_chr${i}_indiv_filtered --extract-if-info "R2>0.8" --maf 0.01 --make-pgen --out data_chr${i}_var_filtered --keep-allele-order

# Remove duplicate variants
${plink2} --pfile data_chr${i}_var_filtered --rm-dup exclude-mismatch --make-pgen --out data_chr${i}_filtered --keep-allele-order

# Convert `pvar/pgen/psam` to `bim/bed/fam` format
${plink2} --pfile data_chr${i}_filtered --make-bed --out data_chr${i}_filtered 

#Reformat pvar file to id maf r2 
awk -v x='#CHROM' '$0~x,EOF''{print $3,$7}' <data_chr${i}_filtered.pvar |perl -pe 's/;/ /g'|awk '{print $1,$3,$4}' |perl -pe 's/ID/ID MAF R2/g' |perl -pe 's/MAF=//g'| perl -pe 's/R2=//g' >data_chr${i}_filtered.info

done

# clean up redundant files
rm data_chr*_dose data_chr*_indiv_filtered*  data_chr*_var_filtered* 

# Merge them into one dataset

for i in {2..23}
do 
    echo "data_chr${i}_filtered"
done > mergefile.txt

${plink2} --bfile data_chr1_filtered --pmerge-list mergefile.txt --make-bed --out data_filtered

# The FIDs in the fam file are set to 0.

head data_filtered.fam
cp data_filtered.fam data_filtered.fam.orig
awk '{print $2,$2,$3,$4,$5,$6}' < data_filtered.fam.orig >data_filtered.fam

# Combine info files into a single file

head -n1 data_chr1_filtered.info > data_filtered.info

for i in {1..23}
do
    awk ' NR>1 {print $0}' < data_chr${i}_filtered.info |cat >> data_filtered.info
done

# check whether the number of variants is the same
wc -l data_filtered.bim
wc -l data_filtered.info
```

The result should be three plink files: `data_filtered.bed`, `data_filtered.bim`, `data_filtered.fam`, and the info file: `data_filtered.info`. Copy them to `DEPP_mqtl/input_data` and set the following variable in your `config` file:

```
bfile_raw="${home_directory}/input_data/data_filtered"
```

and

```
quality_scores="${home_directory}/input_data/data_filtered.info"
quality_type="minimac3"
```