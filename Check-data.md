# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_

**Data upload method:** automated upload to sftp bristol

# Overview: Check data 

We have created a script that will check the data you have deposited and the `config` file parameters to make sure it all looks as expected. 
It checks:
 - Necessary R libraries are present 
 - Creates a list of IDs that are in common between the different data sources 
 - A number of graphs to visualise the raw data. 
 - Summary statistics of the cohort descriptives

To run:

    ./01a-check_data.sh -c /path/to/config/file

**PLEASE NOTE:** It is important to monitor this script as it runs - it will stop with warnings/errors if it encounters problems. Please fix any errors/warnings that are encountered, and re-run the script until it completes without errors.

The script can be run in chunks, so you can fix problems more easily. We recommend running this script interactively. If you have a large methylation dataset, then it is possible to run  `./01a-check_data.sh methylation` in a job. If you run 01a in chunks, please ensure that after each chunk has been successfully executed and any issues have been resolved, you rerun the complete 01a script before proceeding to the next module.
    
    ./01a-check_data.sh -c /path/to/config/file config
    ./01a-check_data.sh -c /path/to/config/file download
    ./01a-check_data.sh -c /path/to/config/file requirements
    ./01a-check_data.sh -c /path/to/config/file genetic
    ./01a-check_data.sh -c /path/to/config/file methylation    
    ./01a-check_data.sh -c /path/to/config/file covariates
    ./01a-check_data.sh -c /path/to/config/file phenotypes_for_ewas
    ./01a-check_data.sh -c /path/to/config/file summary

**IMPORTANT:** The script produces the following plots in the `results/01/` directory. Please check the following plots:
```
cd results/01
```

- The number of SNPs per chromosome (`no_snps_by_chr.pdf`): Please check if the number of SNPs is decreasing among the 22 chromosomes.
- The SNP quality metrics (`snp_quality.pdf`): Please check you filtered correctly on MAF (>0.01) and info (>0.8).
- Age distribution (`age_distribution.pdf`): Please check if you get a histogram for all your samples.
- Sex check on the methylation data: Please check if a plot is generated (`XY_methylation_vs_sex.pdf`).
- Summary statistics of the input data (`cohort_descriptives.RData`). Please check there are no unexpected NAs in there. These statistics will be used for the cohort characteristics Table in the paper.
- The `log.txt` will be used to check you have used the right version of the pipeline


