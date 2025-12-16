# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_, _Script 01a_, _Script 01b_, _Script 01c_ 

**Data upload method:** automated upload to sftp bristol

**Issue report to:** Haotian Tang <haotian.tang@bristol.ac.uk> 

# Module 01d Overview: Conducting Positive and Negative mQTL Controls
Previously [GoDMC](https://www.nature.com/articles/s41588-021-00923-x) project used a full GWAS of `cg07959070` as a positive control, as it showed a strong cis-mQTL in several datasets on chr22, and there hasn’t been a proposal to exclude it from the analyses by probe annotation efforts. However, the paper only focused on European datasets.

In DEEP, we also conduct full GWASs on several mQTL positive controls before we run into the main set of large-scale mQTL GWAS in the analysis phase. This confirms that the pipeline can detect known SNP-CpG associations, validating the analysis setup. We chose `cg07959070` along with nine other CpG sites reported to have strong cis-mQTL signals in non-European populations ([Hawe _et al._ (2022)](https://www.nature.com/articles/s41588-021-00969-x), [Shang _et al._ (2023)](https://www.nature.com/articles/s41467-023-37961-4), and [Kassam _et al._ (2021)](https://academic.oup.com/hmg/article/30/7/603/6129656)). These sites serve as positive controls, where we expect to observe very strong association signals in each mQTL GWAS.

Additionally, after running the positive controls, we shuffle the methylation values across samples to generate negative controls for the same set of CpG sites, which ensures that spurious associations are not being called, improving the robustness of the mQTL analysis.

To run the entire script, use the following command:
```
./01d-mqtl_controls.sh -c /path/to/config/file
```
>[!IMPORTANT]
> **Please check**
> - `cg07959070` should give a signal on chromosome 22. This cis methQTL gives very strong signals in most GoDMC cohorts and is profiled on 450k, EPIC, and EPIC v2 arrays. This CpG survives stringent probe filtering based on Price et al, Chen et al and Naeem et al and no 1000 Genomes SNP was found in the probe sequence or CpG site. Obviously, the strength of the association will depend on the sample size and ancestry of your cohort, and is currently set to a p-value of 0.001.
> - In addition, this GWA is used to check the lambda (genomic inflation statistic) of your data. 
Please check the qqplots and lambdas in the results (`./results/01/01d`) section. 
It is important to check the lambda here before going on, and any lambda above 1.08 is worrying. In general, for a standard GWAS, you would expect to see a lambda below 1.08.
> - For each CpG site, there should be 4 plots for the positive control and 4 plots for the negative control. File names end with `manhattan.pdf`, `nocisChr_manhattan.pdf`, `qqplot.jpeg`, and `nocisChr_qqplot.jpeg`.

Example Manhattan plots for cg07959070:
Positive control:
<img src="images/positive_control_untransformed_cg07959070_manhattan.jpg" />
Negative control:
<img src="images/negative_control_untransformed_NEG_cg07959070_manhattan.jpg" />