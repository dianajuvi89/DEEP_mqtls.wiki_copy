# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_, _Script 01a_

**Data upload method:** automated upload to sftp bristol

# Module 01b Overview: Processing genetic data

Once the imputed genetic data is in the [correct format](Imputed-genetic-data) and in the [correct location](Install-and-set-up), the following tasks need to be performed:
- Liftover to hg38 if hg19
- Extract relevant IDs
- HWE and MAF QC
- Genotype missingness QC
- Remove sexcheck outliers if chr X is available
- Re-code SNP IDs (chr:position_A1_A2 (ascii sorted order))
- Set the maximum allele length to be 70
- Remove duplicate SNPs
- Remove poorly imputed SNPs
- Construct kinship matrices
- Create a pedigree matrix for 'related' samples
- Remove any cryptic relatedness for 'unrelated' data
- Calculate principal components
- Check PC outliers and generate PCA plots
- Create a global PCA plot using PCA loadings
- Check allele frequencies between genetic data and ancestry-specific reference panel.

To do this, run the following script:
```
    ./01b-ancestry_identify.sh -c /path/to/config/file
```
Note that for 'unrelated' samples, we are generating PCs by removing long LD tracts, extracting HapMap3 SNPs, LD pruning, and then calculating PCs on the data after cryptic relateds have been removed. For 'related' samples, we are using a general method implemented in the Rpackage 'GENESIS' for estimating PCs that account for any relatedness. This involves estimating a kinship matrix that is robust to population structure and relatedness ([Manichaikil et al 2010](http://bioinformatics.oxfordjournals.org/content/26/22/2867.long)), estimating principal components on an unrelated subset, and then projecting principal components onto the related subset based on estimated kinships ([Conomos et al 2015](http://onlinelibrary.wiley.com/doi/10.1002/gepi.21896/abstract)).

After you have run this script, please check:
```
cd results/01
```

**IMPORTANT PLEASE CHECK YOUR RESULTS:**
- The `pcaplot.pdf` is a PCA plot on the pruned genetic data. The blue dashed lines define the SD threshold. You can check the number of genetic outliers that are recorded by:
```
grep outlier ./results/01/logs_b/log.txt
```

If you think the outlier threshold should be adjusted, you can change the sd threshold `pca_sd` in the `config` file. If you change the threshold, you need to rerun `01b-ancestry_identify.sh`.
- If you find that your PCA plot contains a V-shaped or poorly clustered distribution of samples when plotting PC1 vs. PC2. Please check the global PCA plot, which uses PCA loadings from global biobank.

- The `easyQC_hrc.rep` shows the number of SNPs that are flipped because they were on the wrong strand as compared to one of the TopMed reference panels ("AlleleChange"). Please find an example here: (https://github.com/genetics-of-dna-methylation-consortium/godmc_phase2/wiki/images//easyQC_hrc.rep).

- The `easyQC_hrc.rep` file also shows you the number of SNPs that are going to be removed. eg, allele mismatches ("AlleleMismatch"). It  also gives a list of SNPs that have a discrepant allele frequency (>0.2) as compared to TopMED ("AFCHECK.numOutlier").

- The `easyQC.multi.AFCHECK.png` plot shows a comparison between allele frequencies of your study as compared to TopMED imputed 1000G samples. Only SNPs with allele frequency differences greater than 0.2 from the reference are plotted. Please read the link for an explanation of this plot [here](http://www.ncbi.nlm.nih.gov/pmc/articles/PMC4083217/figure/F4/). Please find two examples below. Currently, SNVs highlighted in red are NOT removed from the analysis. If all allele frequency differences between your data and the reference panel are ≤ 0.2, the plot will be empty.

[https://github.com/genetics-of-dna-methylation-consortium/godmc_phase2/wiki/Additionalfiles-easyQC.multi.AFCHECK.png](https://github.com/genetics-of-dna-methylation-consortium/godmc_phase2/wiki/Additionalfiles-easyQC.multi.AFCHECK.png).   
