# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_ and _01a_

**Data upload method:** automated upload to sftp bristol


# Module 01f Overview: Functional normalization of DNA methylation data across cohorts
Functional normalization is employed to adjust for unwanted variation in methylation data, such as batch effects or technical variation. When datasets are processed separately, combining them for normalization ensures that these biases are corrected across all cohorts, leading to more accurate and comparable results. This method is crucial for large-scale epigenomic studies where data integration from multiple sources is necessary for comprehensive analysis, e.g., meta-analysis and comparison between cohorts, we would like to conduct pooled analyses across DNA methylation datasets. To reduce associations related to batch effects, we are going to normalise datasets together.

We will use the following approach in meffil: https://github.com/perishky/meffil/wiki/Functional-normalizing-separate-datasets

```
./01f-normalization.sh -c /path/to/config/file shrink
```