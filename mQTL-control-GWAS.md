# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_, _Script 01a_, _Script 01b_, _Script 01c_ 

**Data upload method:** automated upload to sftp bristol

# Overview: Conducting Positive and Negative mQTL Controls
Previously [GoDMC](https://www.nature.com/articles/s41588-021-00923-x) used a full GWAS of `cg07959070` as a positive control, as it showed a strong cis-mQTL in several datasets on chr22, and there hasn’t been a proposal to exclude it from the analyses by probe annotation efforts. However, the paper only focused on European datasets.

In DEEP, we also conduct full GWASs on several mQTL positive controls before we run into the main set of large-scale mQTL GWAS in the analysis phase. This is to confirm that the pipeline can detect known SNP-CpG associations validating the analysis setup. We chose `cg07959070` along with nine other CpG sites reported to have strong cis-mQTL signals in non-European populations. These sites serve as positive controls, where we expect to observe very strong association signals in each mQTL GWAS.

Additionally, after running the positive controls, we shuffle the methylation values across samples to generate negative controls for the same set of CpG sites, which ensures that spurious associations are not being called, improving the robustness of the mQTL analysis.

To run the entire script, use the following command:
```
./01d-mqtl_controls.sh -c /path/to/config/file
```

