# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_, _01a_, _01b_, _01c_, _01d_, and _01e_

**Data upload method:** automated upload to sftp bristol

**Issue report to:** Haotian Tang <haotian.tang@bristol.ac.uk> 

# Overview: Compute haplotype components (HCs) and compare GWAS results of non-correction, PC-corrected, and HC-corrected.

Genetic principal components analysis (PCA) transforms high-dimensional genetic data into a smaller set of uncorrelated PCs that capture most of the variance. PCs capture large-scale genetic ancestry, but cannot account for the local ancestry variation that differs across individual genomic loci ([Atkinson et al.](https://www.nature.com/articles/s41588-020-00766-y)).

Haplotype components (HCs) capture the local, phased structure of genetic variation across the genome by using phased genetic data. This allows them to model subtle ancestry differences that genetic PCs often miss, particularly in cohorts with complex or admixed backgrounds. Theoretically, HCs offer improved control for population stratification and can reduce confounding more effectively than standard genetic PCs in downstream association and epigenetic analyses. Recent work by [Yang and Lawson (2025)](https://journals.plos.org/plosgenetics/article?id=10.1371/journal.pgen.1011883) indicates HCs-correction identifies more genome-wide significant association signals for birthplace and lifestyle-related phenotypes, which are missed by PCs-corrected GWAS.

In Module 1g, we use phased VCF files located in `vcf_dir` (specified in your config file) to compute HCs through [PBWTpaint](https://github.com/richarddurbin/pbwt). We select several CpG sites from [EPISTRUCTURE](https://link.springer.com/article/10.1186/s13072-016-0108-y), a set of methylation markers informative for ancestry inference. For each CpG site, three GWAS models are run: no correction, PC-corrected, and HC-corrected. The pipeline then produces Manhattan and QQ plots for each model, as well as pairwise scatter plots for variants in the cis-region of each CpG site, comparing PC-corrected vs. no correction, HC-corrected vs. no correction, and HC-corrected vs. PC-corrected results.

To run the script, use the following command:
`
./01g_HCs.sh -c /path/to/config/file
`

>[!IMPORTANT]
> **Please check**
> Please check the signals from Manhattan plots, genomic inflation from QQ plots, and scatter plots.