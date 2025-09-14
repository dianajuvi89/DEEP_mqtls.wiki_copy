# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_, _01a_ and _01b_

**Data upload method:** automated upload to sftp bristol

# Overview: Check methylation and phenotypes
This Module processes DNA methylation data and phenotype data for the following aims, including:
- Detect and remove outliers in DNA methylation data for GWAS and/or EWAS.
- Clean, format, and QC phenotype variables.
- Predict epigenetic age and smoking status from methylation
- Predict cell counts by using cleaned DNA methylation data, compare predicted vs measured cell counts (if available), and compare predicted vs predicted cell counts (if multiple cell type references are used).
- Computes PCs for methylation data, and associates the methylation PCs with genetic PCs, batch, and key study and participant characteristics.
- Generate covariates for module 1d and 1e (positive/negative control mQTL analysis and GWAS of genetic PCs).

To run the entire script, use the following command:
```
./01c-check_phenotypes_and_methylation.sh -c /path/to/config/file
```
Importantly, Module 01c is designed to run in chunks, making it easier to fix problems as they arise. We would recommend that you run this script interactively. If you have a large methylation dataset, then it is possible to run  `./01c-check_phenotypes_and_methylation.sh ewas` in a job.
    
    ./01c-check_phenotypes_and_methylation.sh -c /path/to/config/file methy_outlier
    ./01c-check_phenotypes_and_methylation.sh -c /path/to/config/file check_phenotype
    ./01c-check_phenotypes_and_methylation.sh -c /path/to/config/file predict_age_smoking
    ./01c-check_phenotypes_and_methylation.sh -c /path/to/config/file cell_counts
    ./01c-check_phenotypes_and_methylation.sh -c /path/to/config/file ewas
    ./01c-check_phenotypes_and_methylation.sh -c /path/to/config/file meth_pcs
    ./01c-check_phenotypes_and_methylation.sh -c /path/to/config/file combine_covariates

>[!IMPORTANT]
> **Please check**
> Please check plots and output for each of these analyses. 

# Descriptions of each chunk
**methy_outlier**
* Detects and removes methylation outliers using the Tukey method in [ewaff](https://github.com/perishky/ewaff) on the normalized beta matrix.
* Removes CpGs that become >10% missing after outlier handling and saves cleaned methylation objects separately for GWAS and EWAS.
* Produces per‑CpG summary (mean/median/sd) and a cohort descriptives file (sample counts, age/sex summaries).

**check_phenotype**

This script is designed to format the phenotypes based on whether you have designated them as _factor or _numeric, so it is very important that you have added either _factor or _numeric to the end of every variable column name in your phenotype file (See examples in [Phenotype data](https://github.com/MRCIEU/DEEP_mqtls/wiki/Phenotype-data)).

* Produces distribution plots and summary tables for every variable to reveal unexpected values or coding problems (e.g., missingness codes like -9).
* Message any variable with >10% missing values. If this prints and is unexpected, then please check your data.
* Winsorize numeric variables that contain extreme outliers, then re-generate plots and summary statistics for those variables so you can review the effect of winsorization.

**predict_age_smoking**
* Predicts epigenetic age (Hannum clock) and smoking (both mcigarette and Elliott predictors) by using [meffonym](https://github.com/perishky/meffonym).
* Smoking must be coded as a factor (0/1); script will attempt auto-conversion if numeric. Please check the output messages to confirm the pipeline was able to convert this to a factor.
* Generate three prediction plots; check correlation with age (may be weak if age range is limited). We do not expect the correlation with age to be good. 

**cell_counts**
* Predicts cell fractions using appropriate cell reference panels in [EpiDISH](https://github.com/sjczheng/EpiDISH) or [Meffil](https://github.com/perishky/meffil). References include [Unilife](https://genomemedicine.biomedcentral.com/articles/10.1186/s13073-025-01489-7) (blood samples at any age), [Salas](https://www.nature.com/articles/s41467-021-27864-7) (adult blood), [Zheng](https://www.tandfonline.com/doi/full/10.2217/epi-2018-0037) (adult saliva or buccal), and [Middleton](https://www.tandfonline.com/doi/full/10.1080/15592294.2021.1890874) (child saliva).
* Produces per-sample predicted fractions (with prefix per reference), summary statistics, and diagnostic distribution plots.
* Computes Spearman correlation matrices and generates correlation plots and scatter plots comparing predicted and predicted cell counts when multiple references are used and/or comparing predicted and measured cell counts when measured cell counts are available.

**ewas** \
This script runs five EWAS analyses. These are EWAS where we know what to expect - their purpose is to check that the EWAS method and covariates are functioning as we expect in each dataset, and to identify any issues before running the main set of EWAS. We will be able to tell this from the EWAS report HTML files. \
Five EWAS analyses are:
* Smoking EWAS (adults only): should detect AHRR sites
* Age EWAS: should detect many age-associated sites
* Maternal smoking EWAS (children only): should detect MYO1G sites
* Sex EWAS: should detect many sex-associated sites
* Negative control (scrambled sex): should show no true associations; QQ plot inflation must be flagged.

**meth_pcs** \
We aim to identify major drivers of methylation variation so we can select appropriate PC/batch covariates for downstream EWAS and mQTL analyses and detect dataset-specific issues. We generate principal components (PCs) of the methylation data and assess their associations with genetic PCs, batch variables, and core study/participant covariates. \
_**Important note:**_ the `ewas_age_smoking.R` and `methylation_pc_check.R` will be run twice; once with the Salas cell type reference panel, and once with the Unilife panel. Please make sure that you have outputs from these scripts for both Salas and Unilife.
Generate three plots:
* PC1 vs PC2 scatter plot, colored by study variables.
* PC3 vs PC4 scatter plot, colored by study variables.
* Bar plots showing linear-model associations between PCs 1–10 and study variables (which variables explain the most variance in each PC).

**combine_covariates**
* Combine the original covariate file with genetic PCs, all predicted cell counts, and predicted smoking for Modules 1d and 1e.