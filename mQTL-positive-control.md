# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_, _Script 01a_, _Script 01b_, _Script 01c_ 

**Data upload method:** automated upload to sftp bristol

# Module 01d Overview: Conducting mQTL Positive and Negative Controls
Previously [GoDMC](https://www.nature.com/articles/s41588-021-00923-x) paper used mQTL GWAS of `cg07959070` as a positive control, as it showed a strong cis-mQTL in several datasets on chr22, and there hasn’t been a proposal to exclude it from the analyses by probe annotation efforts. However, the paper focused on the European population. Here, we adopt this positive control for mQTL GWAS before we run into the main set of GWAS in the analysis phase. We chose `cg07959070` as well as the other 9 CpG sites that have been reported in non-European populations as a positive control, where we should see very strong signals.