## Genetic data 
### Unphased bfiles
#### Data pre-processing
Prior to running the DEEP pipeline, we require that the genetic data must be
- Imputed to 1000G phase 3 or any other relevant reference panel. Several institutions offer a free imputation service which you can use. By far the easiest way to do imputation now is to use one of these services. See [here](Imputation) for more information.
- Variant positions in hg19/GRCh37 or hg38/GRCh38.
- Filtered to have MAF >= 0.01 and imputation quality score >= 0.8
- **IMPORTANT:** Converted to best guess binary plink format **without a probability threshold**
- All remaining SNPs combined into a single fileset (*i.e.* not a separate fileset for each chromosome)
- No spaces in the file names
- All autosomes
- Chromosome X is optional and should be coded as 23

##### Fam file and Sample IDs

- The fam file should meet the standard plink structure and not contain a header.

- The first column of the `.fam` file are the family IDs. If you have unrelated data then this can be the same as the second column (individual IDs). **If you have related data, please ensure your family IDs correctly capture these relationships.**

- **IMPORTANT:** Please ensure that the second column of the `.fam` file (individual IDs) contains unique sample IDs. These sample IDs should be the same IDs that are used in the covariate and methylation data. Please also ensure that the individual IDs don't contain any underscores. Individual IDs cannot be 0.

- Column 3 and 4 are the fatherID and the motherID. If you don't have parent data please set these IDs to 0. 

- Column 5 is sex; 1 is male, 2 is female.

- Please note that you need to set the phenotype (column 6) to missing i.e. "-9". 

Example fam file for unrelated data:
```
1A 1A  0  0  2 -9
2A 2A  0  0  2 -9    
3A 3A  0  0  2 -9
```

Example fam file for related data (twins, families, mother-offspring etc.):
```
1 1A  0  0  2 -9
1 1B  0  0  2 -9    
2 2A  0  0  2 -9
```

##### Bim file and allele coding


- The bim file should meet the standard plink structure and not contain a header.

- Column 1 is chromosome. Chromosomes should be coded as 1-23, where chromosome X is coded as 23.

- Column 2 is variant name. The pipeline also changes the SNV ids for you (second column) so you don't need to worry about marker names.

- Column 3 should be 0.

- Column 4 should be basepair position for build hg19/b37 or hg38.

- Column 5 and 6: **IMPORTANT:** ensure that your alleles coded using the original impute2 or minimac coding. To perform a meta-analysis across cohorts, alleles should be matching across cohorts. 

- We include SNPs and INDELs. Please note that HRC and maybe other reference panels have SNPs only and no INDELs.

##### Bed file

**IMPORTANT:** We use best guess genotypes without a probability threshold in our pipeline. 
Please make sure you don't filter on a probability threshold as some software packages can't handle missingness properly and will set missing genotypes to the genotypic mean. We use best guess genotypes in our analyses. Please see below how you can prepare best guess files.

##### Imputation quality

We also require imputation quality scores for each SNP. Instructions on how to convert imputed data into the desired format, including your imputation quality file, are provided below.

```
    SNP MAF Info
    rs1 0.02 0.88
```

##### Convert imputed data to bestguess data
  - [Convert minimac3 imputed data to bestguess data (vcf files)](Convert-minimac3-imputed-data-to-bestguess-data-(vcf-files))
  - [Convert impute2 imputed data to bestguess data (.gen/bgen files)](Convert-impute2-imputed-data-to-bestguess-data)
  - [Convert mach/minimac imputed data to bestguess data (.info/.dose or .mlinfo/.mldose files)](Convert-mach-minimac-imputed-data-to-bestguess-data-(.info-.dose-or-.mlinfo-.mldose-files))

### Phased VCF files (specific for module 1g)
#### Data pre-processing
Phased VCF files required for [module 1g][Compute haplotype components](https://github.com/MRCIEU/DEEP_mqtls/wiki/Compute-haplotype-components). If you impute your genetic data by using imputation servers (e.g., Michigan, TOPMed, Sanger), which already provide phased VCF output, please download the phased VCF files directly. See [here](Imputation) for more available Imputation Servers.
Alternatively, if you generate imputed genetic data on your local HPC server, then please phase your genetic data by using [SHAPEIT5](https://odelaneau.github.io/shapeit5/) or other software.

#### VCF format
VCF files must meet the requirement that:
- Variant positions in hg19/GRCh37 or hg38/GRCh38.
- In a phased format.
- Other filtering, such as variant/sample inclusion and imputation quality, is not required for VCF files, as we will perform data cleaning for VCF files by using the sample and variants info from Module 01b, where we performed basic cleaning for bfiles.

To check if your VCF files are phased 
Phased genotypes are typically represented as:
```
0|0, 0|1, 1|0, 1|1
```
Note that the vertical bar “|” indicates phased genotypes, and unphased formats using “/” are not acceptable.

#### Example output files (Michigan Imputation Server)

A typical download structure is as follows:
```
chr1.dose.vcf.gz
chr2.dose.vcf.gz
...
chr22.dose.vcf.gz
```

These files can be used directly as phased VCF input for DEEP module 1g, and should be placed in the vcf_dir specified in your config file [Install and set up](https://github.com/MRCIEU/DEEP_mqtls/wiki/Install-and-set-up).


