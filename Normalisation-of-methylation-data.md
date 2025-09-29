# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_ and _01a_

**Data upload method:** automated upload to sftp bristol


# Overview: Functional normalization of DNA methylation data across cohorts
Functional normalization is employed to adjust for unwanted variation in methylation data, such as batch effects or technical variation. When datasets are processed separately, combining them for normalization ensures that these biases are corrected across all cohorts, leading to more accurate and comparable results. This method is crucial for large-scale DNA methylation studies where data integration from multiple sources is necessary for comprehensive analysis, e.g., meta-analysis, pooled EWAS analyses where the outcome is confounded with cohort. To reduce associations related to batch effects, we are going to normalise datasets together. We use quantiles from methylation probes and control probes to normalise across different datasets.

We will use the following approach in meffil: [Functional normalizing separate datasets](https://github.com/perishky/meffil/wiki/Functional-normalizing-separate-datasets).

**Step 1**: Obtain QC objects from each dataset and perform cross-cohort functional normalization.
<img src="images/shrink.png" alt="shrink" width="500"/>


**Step 2**: Send the cross-cohort functionally normalized dataset back to each cohort.
<img src="images/expand.png" alt="expand" width="500"/>
To obtain QC objects from your dataset, please run the following command:
```
./01f-normalization.sh -c /path/to/config/file shrink
```
This will generate a `qc_objects_shrink.rda` in `/results/01` and `qc_objects.rda` in `/processed_data/methylation_data`. The `qc_objects_shrink.rda` does not contain ID information and will be uploaded to Bristol SFTP server for cross-cohort functional normalization.
On Bristol SFTP server, we will estimate the correct number of control probe principal components to include in the normalization and then use (meffil.normalize.quantiles())[https://github.com/perishky/meffil/blob/master/R/normalize-quantiles.r] to normalize the pooled objects from all DEEP cohorts. 
After you receive cross-cohort normalized data from Bristol, please put into `input_data` folder and then run the following command:
```
./01f-normalization.sh -c /path/to/config/file expand
```