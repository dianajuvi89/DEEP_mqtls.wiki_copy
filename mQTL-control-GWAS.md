# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_, _Script 01a_, _Script 01b_, _Script 01c_ 

**Data upload method:** automated upload to sftp bristol

# Module 01d Overview: Conducting mQTL Positive and Negative Controls
Previously [GoDMC](https://www.nature.com/articles/s41588-021-00923-x) paper used mQTL GWAS of `cg07959070` as a positive control, as it showed a strong cis-mQTL in several datasets on chr22, and there hasn’t been a proposal to exclude it from the analyses by probe annotation efforts. However, the paper only focused on the European population. 
In DEEP, we also conduct positive control mQTL full GWAS before we run into the main set of large-scale mQTL GWAS in the analysis phase. We chose `cg07959070` as well as the other 9 CpG sites that have been reported in non-European populations as positive controls, where we should see very strong signals in each mQTL GWAS.
In addition, after we conduct the positive controls, we shuffle the methylation of the sample and run the negative controls for the sample set of CpG sites.

To run the entire script, use the following command:
```
./01d-mqtl_controls.sh
```

