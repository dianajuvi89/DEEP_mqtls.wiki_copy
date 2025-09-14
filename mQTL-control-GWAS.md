# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_, _Script 01a_, _Script 01b_, _Script 01c_ 

**Data upload method:** automated upload to sftp bristol

# Module 01d Overview: Conducting mQTL Positive and Negative Controls
Previously [GoDMC](https://www.nature.com/articles/s41588-021-00923-x) paper used mQTL GWAS of `cg07959070` as a positive control, as it showed a strong cis-mQTL in several datasets on chr22, and there hasn’t been a proposal to exclude it from the analyses by probe annotation efforts. However, the paper only focused on the European population. \
In DEEP, we also conduct positive control mQTL full GWAS before we run into the main set of large-scale mQTL GWAS in the analysis phase, to confirm that the pipeline can detect known SNP-methylation associations, validating the analysis setup. We chose `cg07959070` along with nine other CpG sites reported to have strong cis-mQTL signals in non-European populations. These sites serve as positive controls, where we expect to observe very strong association signals in each mQTL GWAS.

Additionally, after running the positive controls, we shuffle the methylation values across samples to generate negative controls for the same set of CpG sites, which ensures that spurious associations are not being called, improving the robustness of the mQTL analysis.

To run the entire script, use the following command:
```
./01d-mqtl_controls.sh
```

