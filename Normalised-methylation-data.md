## Within-cohort DNA methylation data preprocessing

This page outlines the steps for formatting DNA methylation data, which need to be performed before running the DEEP pipeline.

### Option 1 (preferred)
Ideally, we would prefer it if you normalise and QC your data using the R [meffil](https://github.com/perishky/meffil) package. **Please make sure you have installed version 1.5.0 or higher.** See [here](https://github.com/perishky/meffil/wiki/Installation) for installation instructions. You can check the version like this:

```
packageVersion("meffil")
``` 

Meffil has been optimised for speed and memory, and instructions on how to do this can be found here:

1. [Perform sample QC](https://github.com/perishky/meffil/wiki/Sample-QC)
2. [Normalise QC'd samples](https://github.com/perishky/meffil/wiki/Functional-normalization)

### Option 2

While we prefer you follow our recommended normalisation approach, we do not enforce this. If not meffil, then we would prefer that you would use an alternative functional normalisation method. If you prefer to use an alternative normalisation method then please ensure that the DNA methylation data you provide meets the following criteria:
- Beta values (i.e. proportions) should be used to quantify DNA methylation level.
- The methylation data is an R numeric matrix object where each row is a CpG site and each column is a sample. 
- The rownames must be the unique `cg` identifiers and the column names are the IDs that correspond to the samples, and that match to sample IDs in the other datasets (genetic, covariate etc).

## Data format

Regardless which normalisation method you use the following need to be adhered to:

- You should avoid spaces in your file name.
- You should keep sites from chrX and chrY.
- You should exclude the SNP probes with IDs that start with rsXXXX (65 on the 450K array and 59 on the EPIC array).
- You should keep nonspecific binding probes, probes with SNPs in their sequence, multimapping probes. We will filter out these probes at the end.
- Underscores should not be used in the sample IDs.
- The pipeline removes outliers with the Tukey method in the processing methylation module so you shouldn't have NAs in your methylation dataset.
- The methylation matrix should be saved as a `.RData` file (each row is a CpG site and each column is a sample) and the methylation matrix object should be called `norm.beta`. e.g. in R:

```
save(norm.beta, file="/path/to/deep/input_data/methylation.RData")
```

## Cross-cohort DNA methylation data preprocessing
Normalised methylation data across cohorts is crucial for large-scale DNA methylation studies.

The cross-cohort functional normalization occurs in two stages:

### Central quantile alignment ([Module 01f Step 1](https://github.com/MRCIEU/DEEP_mqtls/wiki/Cross%E2%80%90cohort-normalisation-of-methylation-data))

After cohorts upload their within-cohort processed methylation data, the DEEP core team performs a central step using only quantile information, which does not include any individual-level data. The central team aligns quantiles across cohorts to remove systematic technical differences.

### Local cross-cohort normalization by each cohort ([Module 01f Step 2](https://github.com/MRCIEU/DEEP_mqtls/wiki/Cross%E2%80%90cohort-normalisation-of-methylation-data))

The centrally aligned quantiles are then returned to each cohort. Using these quantiles, cohorts complete the full cross-cohort functional normalization locally, on their own individual-level methylation data.

After all cohorts complete this step, the resulting harmonised methylation datasets will be used in future pooled EWAS analyses through  DataSHIELD in the Analysis stage
For the QC stages, cohorts only need to complete the within-cohort preprocessing and upload the required quantile files via the Bristol SFTP system.