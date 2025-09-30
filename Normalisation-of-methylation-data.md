# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_ and _01a_

**Data upload method:** automated upload to sftp bristol


# Overview: Functional normalization of DNA methylation data across cohorts
Functional normalization is employed to adjust for unwanted variation in methylation data, such as batch effects or technical variation. When datasets are processed separately, combining them for normalization ensures that these biases are corrected across all cohorts, leading to more accurate and comparable results. This method is crucial for large-scale DNA methylation studies where data integration from multiple sources is necessary for comprehensive analysis, e.g., meta-analysis, pooled EWAS analyses where the outcome is confounded with the cohort. To reduce associations related to batch effects, we are going to normalise datasets together. To minimize batch-related associations, we will normalize the datasets jointly, using quantiles from both methylation probes and control probes. This module aims for later pooled EWAS analysis.

We will use the following approach in meffil: [Functional normalizing separate datasets](https://github.com/perishky/meffil/wiki/Functional-normalizing-separate-datasets).

## **Step 1**: Obtain QC objects from each dataset and perform cross-cohort functional normalization.
<img src="images/shrink.png" alt="shrink" width="500"/>

To obtain shrunk QC objects from your dataset, please run the following command:
```
./01f-normalization.sh -c /path/to/config/file shrink
```
This will generate a `qc_objects_shrink.rda` in `/results/01` and `qc_objects.rda` in `/processed_data/methylation_data`. The `qc_objects_shrink.rda` excludes ID information and should be uploaded to the Bristol SFTP server for cross-cohort functional normalization.


On the Bristol SFTP server, we will estimate the optimal number of control probe principal components to include in the normalization and use [meffil.normalize.quantiles()](https://github.com/perishky/meffil/blob/master/R/normalize-quantiles.r) to normalize the pooled objects from all DEEP cohorts. Then, we send the cross-cohort functionally normalized quantiles back to the corresponding cohort.


## **Step 2**: Restore information to normalized QC objects derived from shrunk QC objects  
<img src="images/expand.png" alt="expand" width="500"/>


Do not proceed until you have received the cross-cohort normalized quantiles from Bristol. Once received, please place your `norms.rda` file in the `input_data` folder and then run the following command:
```
./01f-normalization.sh -c /path/to/config/file expand
```