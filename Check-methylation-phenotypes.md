# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_, _01a_ and _01b_

**Data upload method:** automated upload to sftp bristol

The script can be run in chunks so you can fix problems more easily. We recommend to run this script interactively. If you have a large methylation dataset, then it is possible to run  `./01a-check_data.sh methylation` in a job.
    
    ./01c-check_phenotypes_and_methylation.sh methy_outlier
    ./01c-check_phenotypes_and_methylation.sh check_phenotype
    ./01c-check_phenotypes_and_methylation.sh predict_age_smoking
    ./01c-check_phenotypes_and_methylation.sh cell_counts
    ./01c-check_phenotypes_and_methylation.sh ewas    
    ./01c-check_phenotypes_and_methylation.sh meth_pcs
    ./01c-check_phenotypes_and_methylation.sh combine_covariates