# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_, _01a_ and _01b_

**Data upload method:** automated upload to sftp bristol

# Module 01c overview: Check methylation and phenotypes
Module 03 processes DNA methylation data and phenotype data for the following aims, including:
- Detect and remove outliers in DNA methylation data for GWAS and/or EWAS.
- Clean, format, and QC phenotype variables.
- Predict epigenetic age and smoking status from methylation
- Predict cell counts by using cleaned DNA methylation data, compare predicted vs measured cell counts (if available), and compare predicted vs predicted cell counts (if multiple cell type references are used).
- Computes PCs for methylation data, and associates the PCs with genetic PCs, batch, and key study and participant characteristics.
- Generate covariates for Module 01d (mQTL positive/negative control analysis).

To run the entire script, use the following command:
```
./01c-check_phenotypes_and_methylation.sh
```
Importantly, Module 01c is designed to run in chunks, making it easier to fix problems as they arise. We would recommend that you run this script interactively. If you have a large methylation dataset, then it is possible to run  `./01c-check_phenotypes_and_methylation.sh ewas` in a job.
    
    ./01c-check_phenotypes_and_methylation.sh methy_outlier
    ./01c-check_phenotypes_and_methylation.sh check_phenotype
    ./01c-check_phenotypes_and_methylation.sh predict_age_smoking
    ./01c-check_phenotypes_and_methylation.sh cell_counts
    ./01c-check_phenotypes_and_methylation.sh ewas
    ./01c-check_phenotypes_and_methylation.sh meth_pcs
    ./01c-check_phenotypes_and_methylation.sh combine_covariates

**remove_outliers**



**check_phenotypes**

This script is designed to format the phenotypes based on whether you have designated them as _factor or _numeric, so it is very important that you have added either _factor or _numeric to the end of every variable column name in your phenotype file.

The script will generate plots and summary statistics of your data, to check that the distributions look as expected, and to check for any improbable values or missing codes that have not been removed (for example, a missingness code of -9 that has not been recoded to NA).

It will print a warning message if there is >10% missing data for any variable - if this prints and is unexpected, then please check your data.

It will then winsorize numeric variables which contain outliers, and re-plot and generate new summary statistics for any variables that are winsorized - again, please check these plots.

Finally, it saves out the winsorized data.


**predict_age_smoking**

This script predicts epigenetic age (using the Hannum clock), and smoking (using both mcigarette and Elliott predictors, which we will test to see which performs best across DEEP studies). 

We care coding smoking as a factor (no=0 yes=1) across DEEP studies - if smoking is coded as a numeric variable for your study, please check the output messages to confirm the pipeline was able to convert this to a factor.

This script will output three plots, one for each predictor. Please check the correlation with age - if there is limited variation in age in your dataset, we do not expect the correlation with age to be good. 

**ewas_age_smoking**

This script runs five EWAS analyses. These are EWAS where we know what to expect - their purpose is to check that the EWAS method and covariates are functioning as we expect in each dataset, and to identify any issues before running the main set of EWAS. We will be able to tell this from the EWAS report html files.

The first is a smoking EWAS. In the meffil EWAS report, please check that this EWAS identifies sites including AHRR as associated with smoking. This should not run for child studies.

The second is an age EWAS. Please check the QQ plot; this should identify lots of sites associated with age.

The third is a maternal smoking EWAS - this should only run if this is a child study. Please check there are sites associated including in MYO1G.

The fourth is an EWAS of sex. Please check the QQ plot; this should identify lots of associations.

The fifth is a negative control; it scrambles the sex variable and runs an EWAS of that. Because the sex variable is randomised, this EWAS should not identify sites associated with the randomised variable. If there is inflation of the QQ plot, please flag this to us.

**methylation_pc_check**

This script generates principal components of the methylation data, and associates the PCs with genetic PCs, batch, and key study and participant characteristics. This is so we can identify what the big drivers of methylation variation are in each dataset. This will help us control for the right batch and study variables in our main analyses. It also helps identify any issues there might be.

This script will create three plots. The first two plot PC1 vs PC2, and PC3 vs PC4, coloured by study variables. The third contains bar plots of linear model associations between the first 10 PCs and study variables - this helps us know which variables are most associated with each of the methylation PCs.

_**Important note:**_ the ewas_age_smoking.R and methylation_pc_check.R should each run twice; once with the Salas cell type reference panel, and once with the Unilife panel. Please make sure that you have outputs from these scripts for both Salas and Unilife.




