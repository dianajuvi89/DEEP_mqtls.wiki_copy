# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** _in development_

**Prerequisite scripts:** _script 00, script 01, script 02a, 02b_

**Data upload method:** automated upload to sftp bristol


# Module overview

Module 03 processes DNA methylation data for the following modules, including:

- scripts to remove outliers and generate covariates (script 03a) 
- scripts to adjust for covariates (scripts 03b-03e)
- scripts to check positive controls (scripts 03f-03g)

Please make sure you run the required scripts for the projects you are participating. A script flowchart is provided [here](Script-Flowchart). 

Briefly,

* **Cohort should run 03a-03g for mQTL analysis (module 04), variance mQTL (module 05)**

If you run 03a-03g, run `bash check_upload.sh 03 check` and `bash check_upload.sh 03 upload` after 03a-03g.

If you run 03a-03d, run `bash check_upload.sh 03d check` and `bash check_upload.sh 03d upload` after 03a-03d.

If you only run 03a, run `bash check_upload.sh 03a check` and `bash check_upload.sh 03a upload` after 03a.

# Scripts description
## Script 03a - Processing DNA methylation data & Generate covariates

You have reformatted the data files for the SNP data. Now we will reformat and process the methylation data files. This section implements the following processing steps.

### Remove outliers

- We remove outliers using the Tukey method. The [ewaff.handle.outliers() function](https://github.com/perishky/ewaff/blob/master/R/handle-outliers.r) automates this. The default of removing anything outside (25th percentile - 3*IQR, 75th percentile + 3*IQR) is fairly common for most consortia EWAS meta-analyses. We also remove CpGs with >10% of samples outliers.

### Generate covariates and derived variables

- We create DNAm scores using the following methods:
    - smoking using method in [Elliott, H.R., et al. (2014)](https://doi.org/10.1186/1868-7083-6-4)
    - Cellular proportions for 12 blood cell types using reference data from [Salas, L.A. et al. (2022)](https://rdcu.be/dvfJu) and method in [EpiDISH](https://rdcu.be/dvfI8)

### Calculation of smoking score

Using loci robustly associated with tobacco smoking we will generate a proxy for smoking exposure from the DNAm data. The output here is a single continuous variable that is unitless but is elevated in current smokers, intermediate in former smokers, correlates with pack years, and decreases over time following cessation.

### Calculation of predicted cell counts

We estimate blood cell counts from normalized betas for 12 cell counts (Salas 2022): neutrophils, eosinophils, basophils, monocytes, naive and memory phenotypes of B cells, CD4 T cells, and CD8 T cells, regulatory T cells, and natural killer cells.

We generate the following output:
- histograms of cell counts
- comparison of predicted vs measured cell counts (if measured cell counts are provided)
- table of cell counts (mean +/- sd)

Additionally, cell counts will be used as covariates in further analysis.

### Create covariate files

We combine covariates in a single file. The covariates are: age, sex, optional covariates, predicted cell counts, predicted smoking, genetic PCs.

To do this simply run

```
    ./03a-methylation_variables.sh
```

This script should run very quickly (e.g. less than 5 minutes for 100 individuals and 6 min on ~1800 samples), and generate the necessary files in the `processed_data/methylation_data` folder.

>[!IMPORTANT]
> **Please check the following graphs:**
> - `results/03/smoking_prediction.pdf` - shows smoking predictor distribution.
> - `results/03/cellcounts_plot.pdf` - shows cell count distributions. Please note that there maybe zeros in the cell counts (CD8T and eosinophils) so the transformations don't look great. Please note we are not running a GWAS on eosinophils but only use the untransformed eosinophils as a covariate in the analysis.
> - `results/03/cor_plot.pdf` - shows correlation between directly measured cell counts and predicted cell counts.
>
> - `results/03/cellcounts_summary.txt` - comprises descriptives for each cell type proportion
> - `results/03/methylation_summary.RData` - comprises methylation means and outliers for each CpG site
> - `results/03/cohort_descriptives_commonids.RData` - comprises number of individuals, SNPs, CpGs, males/females and mean age
>
> **Please also check the log file:** 
> - `results/03/logs_a/log.txt` - The last sentence should say `Successfully created methylation-related variables`
> - The script also generates a covariate file which you can see here `head ${covariates_combined}.txt`

## Scripts 03b-03e - Adjustment of methylation values 

Next we will pre-adjust the methylation values for all possible covariates for mQTL, variance mQTL and EWAS analysis. For the mQTLs we will reduce the possibility of type 1 errors by transforming the methylation values using rank inverse normal transformation. We will use untransformed methylation data for variance mQTL data. This is a more computationally intensive process and you have the option to split it across multiple threads on a single computer, or across multiple threads and multiple nodes on a cluster (though this second option requires some script input from you). 

### Overview of the steps we are about to perform
1. Generate Rank Inverse-normal transformed value of each methylation probe and untransformed value of each methylation probe.

2. Fit age, sex, optional covariates, predicted smoking, predicted cell counts, genetic principal components as fixed effects, and family relatedness as a random effect if it is family data, against each methylation probe and keep the normalised residuals. Covariates with same value across all samples will be removed (eg. age will be removed if all samples are at the same age). Set NAs to probe mean.

3. Generate Rank Inverse-normal transformed value and untransformed value of residuals from step 2.

4. Estimate the methylation principal components using the 20000 most variable methylation probes for both transformed and untransformed methylation probes, retain the first `n` PCs that individually explain at least 1% of the methylation variation.

5. Run a GWAS against each of the retained methylation PCs and discard any PCs that have evidence for a genetic effect (p < 1e-7).

6. Fit the remaining non-genetic methylation PCs against each of the methylation probes from step 2 and retain the residuals. Set NAs to probe mean.

7. Generate Rank Inverse-normal transformed value and untransformed value of residuals from step 6.

These residuals have now been adjusted for measured covariates, estimated cell counts, estimated smoking, genetic relatedness and structure, and estimates of unmeasured confounders. Therefore the residual variance should be minimised as much as possible without losing genetic variance.

### Methylation values adjustment1
To generate residuals of the first-step adjustment (step 1-3) run:

```
    ./03b-methylation_adjustment1.sh ${meth_chunks}
```

This will parallelise across `$nthreads` (which was set in your config file). Please note `$nthreads` needs to be smaller than the number of chrY probes. `$nthreads=16` works well in 1500 samples. The input `${meth_chunks}` represents which chunk of methylation probes are input to 03b script (which was set in your config file, see [here](Running-script-3b-and-3e-on-a-cluster) for instructions on how to parallelise this analysis across multiple nodes on a cluster. 

For 1 of the 100 chunk jobs running on 16 cores, it took about 2 hours to run with 100 related samples but it took 1 hour to run with ~1800 unrelated samples. You can check your log files on `/results/03/logs_b`. Please follow the instructions [here](Running-script-3b-and-3e-on-a-cluster) to check the scripts output. You can change the number of chucks in the config file; if it is very slow please increase the number. Large cohorts (a few thousand participants) may benefit from using around 1000 chunks.

### Estimate methylation PCs
In order to perform step 4, run:
```
    ./03c-methylation_pcs.sh
```

This step took 15 minutes with a sample of 100 individuals and 15 minutes with a sample of ~1800 individuals. 

Please check the log file generated in 3c: 
```
    grep "Successfully generated non-genetic methylation PCs" ./results/03/logs_c/log.txt
    cat ./results/03/logs_c/log.txt
```
The log file will include information about how many non-genetic methylation PCs can explain at least 1% methylation variance. 
>[!IMPORTANT]
> * If there is no methylation PC satisfying the 1% threshold in a dataset (eg. transformed or untransformed methylation dataset), no output file will be generated for this dataset.

### Observe non-genetic PCs
In order to perform step 5, run:
```
    ./03d-non_genetic_methylation_pcs.sh
```
Again, it parallelises across `$nthreads` (for the GWAS stage). In the `config` file we have currently set the GWA significance threshold to 1e-7 (`meth_pc_threshold="1e-7"`). This means that any GWA signals with a p<1e-7 are considered as genetic signals. These "genetic" PCs are ignored and not regressed out from the methylation data. The maximum number of "non-genetic" PCs that is regressed out is set to 20 in the `config` file (`n_meth_pcs="20"`)

Please check the log file generated in 03d: 
```
    grep "Successfully generating non-genetic methylation PCs" ./results/03/logs_d/log.txt
    cat ./results/03/logs_d/log.txt
```
>[!IMPORTANT]
> * If there is no methylation PC file generated by 03c for a dataset (eg. transformed or untransformed methylation dataset), 03d will skip for this dataset.

### Methylation values adjustment2
In order to run step 6, run the following:
```
    ./03e-methylation_adjustment2.sh ${meth_chunks}
```
This should take roughly the same amount of time as the `03b-methylation_adjustment1.sh` script on unrelated data. Again, it can be split across multiple nodes on a cluster, see [here](Running-script-3b-and-3e-on-a-cluster) for instructions. The adjustment 2 of sex chromosomes will only be performed when the ${meth_chunks} is 1 because of the much smaller number of CpGs on sex chromosomes.


>[!IMPORTANT]
> If none of your methylation PC of a certain dataset is non-genetic PC. Here is what the pipeline does -
Take ```untransformed_methylation_adjusted.RData``` as an example
> * 03e and aggregate_adjustment2.sh scripts will skip the PC adjustment and aggregation steps for this dataset. 
> * ```untransformed_methylation_adjusted.RData``` will be copied to ```untransformed_methylation_adjusted_pcs.RData```

**Please raise a issue or email us if you're unsure about your output from 03b-03e.**

## Scripts 03f-03g - Perform positive control 
We use CpG "cg07959070" as a positive control CpG which is supposed to give a cis-mQTL signal on chromosome 22. 

In the next step we convert our methylation data to mQTL format.

```
    ./03f-convert_methylation_format.sh
```

Finally we run one CpG GWA as a positive control as a final check and generate plots. 
```
    ./03g-perform_positive_control.sh
```

>[!IMPORTANT]
> **Please check**
> - `cg07959070` should give a signal on chromosome 22 as this cis methQTL was found in at least 3 cohorts (`p<1e-8`, index SNP `22:49660223 - hg19` `chr22:50053871:SNP hg38` or rs12485195) and is profiled on 450k, EPIC and EPIC v2 arrays. This CpG survives stringent probe filtering based on Price et al, Chen et al and Naeem et al and no 1000 Genomes SNP was found in the probe sequence or CpG site. Obviously the strength of the association will depend on the sample size of your cohort and is currently set to a pvalue of 0.001.
> - In addition this GWA is used to check the lambda (inflation statistic) of your data. 
Please check the qqplots and lambdas: `positive_control_untransformed_cg07959070_qqplot.jpeg`,`positive_control_untransformed_cg07959070_nocisChr_qqplot.jpeg`,  `positive_control_transformed_cg07959070_qqplot.jpeg` and `positive_control_transformed_cg07959070_nocisChr_qqplot.jpeg` in the results (`./results/03/`) section. 
It is important to check lambda here before going on and any lambda above 1.08 is worrying. In general for a standard GWA you would expect to see a lambda below 1.08.



### Upload the results

To check that everything ran successfully, please run:

```
./check_upload.sh 03 check
```

This should tell you that `Section 03 has been successfully completed!`. Now please upload the results like this:

```
./check_upload.sh 03 upload
```

It will make sure everything looks correct and connect to the sftp server. It will request your password (this should have been provided to you along with your username). Once you have entered your password it will upload the results files from section 03.

This procedure will be repeated at the end of each section.