# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_, _Script 01a_

**Data upload method:** automated upload to sftp bristol

# Processing genetic data

Once the imputed genetic data is in the [correct format](Imputed-genetic-data) and in the [correct location](Install-and-set-up), the following tasks need to be performed:

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
- Global PCA plot using PCA loadings
- Check allele frequencies between genetic data and ancestry-specific reference panel.

To do this, run the following script:
```
    ./01b-ancestry_identify.sh
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

If you think the outlier threshold should be adjusted, you can change the sd threshold `pca_sd` in the `config` file. If you change the threshold, you need to rerun `01b-process_genetic_data.sh`.\
- If you find your PCA plot, which contains a V-shaped or poorly clustered distribution of samples when plotting PC1 vs. PC2. Please check the global PCA plot, which uses PCA loadings.

- The `easyQC_hrc.rep` shows the number of SNPs that are flipped because they were on the wrong strand as compared to HRC ("AlleleChange"). Please find an example here: (https://github.com/genetics-of-dna-methylation-consortium/godmc_phase2/wiki/images//easyQC_hrc.rep)

- The `easyQC_hrc.rep` file also shows you the number of SNPs that are going to be removed. eg. allele mismatches ("AlleleMismatch") and also SNPs that have a discrepant allele frequency (>0.2) as compared to HRC ("AFCHECK.numOutlier").
- The `easyQC.multi.AFCHECK.png` plot shows a comparison between allele frequencies of your study as compared to HRC. Only outlying SNPs that differ > 0.2 in terms of allele frequency from the reference frequency, will be plotted. Please read the link for explanation of this plot [here](http://www.ncbi.nlm.nih.gov/pmc/articles/PMC4083217/figure/F4/). Please find two examples below. All the SNVs in red will be removed from the analysis. You will see an empty plot if there are no SNPs to remove.

[[Additionalfiles/easyQC.multi.AFCHECK.png]].   

It will ensure everything looks correct and connects to the SFTP server. It will request your password (this should have been provided to you along with your username). Once you have entered your password, it will upload the results files from section 02.