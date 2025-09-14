# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_ and _01a_

**Data upload method:** automated upload to sftp bristol


### Overview: Functional normalization of DNA methylation data across cohorts

In addition to a meta analysis approach we would like to conduct pooled analyses across DNA methylation datasets. To reduce associations related to batch effects we are going to normalise datasets together.

We will use the following approach in meffil: https://github.com/perishky/meffil/wiki/Functional-normalizing-separate-datasets

```
./01f-normalization.sh -c shrink
```