This page is written for those who are part of the core team that are updating the pipeline or adding new analyses. At present just the core group are able to contribute to the pipeline - if you are interested in joining this group please contact either Josine Min (Josine.Min@bristol.ac.uk) or Hannah Elliott (Hannah.Elliott@bristol.ac.uk).

This page contains two levels of information. First it outlines how edits are to be made. Second it outlines the architecture of the code and provides some orientation to what is already included and where it can be found. 

# Design of the pipeline

## Background to the pipeline:

We chose to set up a new github repository for DEEP. However, lots of the original scripts/instructions etc were copied over from [GoDMC](https://github.com/genetics-of-dna-methylation-consortium/godmc_phase2). It is worth taking a look there first to see if there is a previous script you can adapt. The pipeline is hosted on git as it is a fantastic tool for collaborative software development. If your never have used git before then you can look at some training pages here: [git course](https://www.udacity.com/course/version-control-with-git--ud123) and  [introducing git](https://chryswoods.com/introducing_git/). Please use the [issues](https://github.com/MRCIEU/DEEP_mqtls/issues) to functionality to report bugs, to document what you are working on and to keep up to date with the development status of the pipeline.



## Pipeline structure and logistics

The pipeline works because we require a consistent data format and directory structure. It is designed to be run on the command line by executing a series of sequential bash scripts. Each of these scripts is numbered, where the number refers to the stage of the pipeline. Where there are multiple bash scripts for a stage, these are and then lettered to refer to the order of the scripts within that stage (e.g. `02axxx.sh`, `02bxxx.sh`). These stages are defined and referred to throughout the [wiki](https://github.com/MRCIEU/DEEP_mqtls/wiki). The bash scripts co-ordinate the pre-processing steps and analyses by calling on other scripts, software programmes, input data and reference data which are located in the following folders:

* `input_data` this is were the analysts will put the methylation, genotype and covariate data for their cohorts. 

* `processed_data` this is where outputs of the pre-processing steps are stored. There are a number of sub-folders within this folder. This is the locatioon for any temporary/intermediate files that need to be used later on in the pipeline but won't be uploaded as results. 

* `resources` this is where associated data/binaries/scripts that the pipeline needs to run are to be stored (e.g. allele frequencies of SNPs, weights for calculating smoking scores, R scripts that are called in the shell scripts). If you use software other than R then you need to add your binaries here: `/resources/bin`. Other resources are organised into folders.

* `results` this is where all results files are to be stored. It is files in this folder that will be uploaded and shared across cohorts. Please note you are not allowed to add any individual level data here. You can only add summary statistics here. This can include plots.

Note analysts should only be expected to deposit data in the `input_data` folder. Anything you want to share with analysts should go in the `resources` folder. Git doesn't work well with large files in this case you may need to host these somewhere else and have the analysts download them. Commands for this should be included in `01-check_data.sh`.

As well as the consistent data format and directory structure there are two key files that make the pipeline run.

1. `config` file. This is where the cohort analysts can provide further details on their cohort that are needed for the pipeline. It is a series of variables that they need to complete. We provide an example of what this should look like. It is worth looking in here to see what variables are already defined that you may need to make use of. 

2. `./resources/parameters` file. This is not edited by the analyst, but is used by the developers to specify the location of log files, results files, software, binaries etc as bash variables. If you see a variable referred to in a script it is likely defined in this file. 

## Other tips

1. Familiarise yourself with what is already in the pipeline - it is probably that many of the covariates have already been derived for example. Please reuse as much as possible and minimise replication. If you have an objection to the way something have been implemented please raise an issue for discussion. 
2. You need to write a [wiki](https://github.com/MRCIEU/DEEP_mqtls/wiki) page with instructions. This needs to explain to the analyst what this stage of the pipeline is doing. Please include here your contact details for analysts to use if they run into trouble.
3. We suggest you set up notifications for issues so you can be kept informed of changes to the pipeline.  
4. You need to add installation instructions for your R packages [here:] (System-requirements)
5. In `resources/datacheck/requirements.R` you need to add the R packages that analysts need for their analysis
6. In `resources/datacheck/covariates.R` we check covariates. Genetic PCs will be generated in script 02.
7. In `resource/logs/version.txt`, specify the required version of your scripts. The required version of the script should be equal to or earlier than the latest tag of the pipeline. If the pipeline has multiple tag versions, make sure to update the version of your script accordingly.
8. Bare in mind we have cohorts with related samples so your analyses need to consider both related and unrelated data.
9. We also have cohorts with single sex eg females only or same age so your scripts need to consider this.
9. Each stages should implement a check to see whether all results files have been generated. There is a generic bash script for this purpose which needs to be edited for each stage. This runs with the command `check_upload.sh 04 check`.
10. Each stage needs to implement it's own  upload command to upload all files from the `results` folder to the desired upload location. It is your responsibility to ensure that all the necessary files are uploaded and uploaded to the correct location. For some modules we use `check_upload.sh 04 upload`. 
11. Please note that for upload you need to encrypt the results files. You can use `gpg -v -o cohortname_01.tgz.aes -c --cipher-algo AES256 cohortname_01.tgz`. For decryption you can use `gpg --pinentry-mode loopback --decrypt --output "cohortname_01_decrypted.tgz" "cohortname_01.tgz.aes"`.
11. We already have a script to generate a table with cohort descriptives. This is in script 01 here: https://github.com/MRCIEU/DEEP_mqtls/blob/main/resources/datacheck/collect_descriptives.R. Please add to this if needed.
12. We advise that each script is tested by other developers. Please get in touch when you are ready for this. 
13. We advise that all code is reviewed by other developers. Please get in touch when you are ready for this. 
14. Please note that all files in the results are uploaded. Please check no individual level data is present in the results folder. 

## Data preprocessing

Most analyses will need running modules 01-03. After running module 03 (03a-03f), you will get

1/ Methylation data of autosomal CpGs

`transformed_methylation_adjusted.RData`

`untransformed_methylation_adjusted.RData`

`transformed_methylation_adjusted_pcs.RData`

`untransformed_methylation_adjusted_pcs.RData`

2/ Methylation data of sex chromosome CpGs

`transformed_methylation_adjusted.[Female.chrX / Male.chrX / Male.chrY].RData`

`untransformed_methylation_adjusted.[Female.chrX / Male.chrX / Male.chrY].RData`

`transformed_methylation_adjusted_pcs.[Female.chrX / Male.chrX / Male.chrY].RData`

`untransformed_methylation_adjusted_pcs.[Female.chrX / Male.chrX / Male.chrY].RData`

## Details about these RData files

`RData` starting with `transformed_methylation_adjusted` and `untransformed_methylation_adjusted` are outcome of 03b (adjusting methylation for age, sex, smoking, cells). These covariates are in `./processed_data/methylation_data/all_covariates.txt`).
`RData` starting with `transformed_methylation_adjusted_pcs` and `untransformed_methylation_adjusted_pcs` are outcome of 03d (adjusting methylation for covariates in `./processed_data/methylation_data/all_covariates.txt` and methylation PCs).

The differences between `transformed` RData and `untransformed` RData are:

transformed RData: RINT(CpG) -> adjust covariates -> RINT(residuals)

untransformed RData: CpG -> adjust covariates and retain residuals

Please choose the appropriate data for the following development.




