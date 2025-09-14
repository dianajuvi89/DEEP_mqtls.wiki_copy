# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_, _01a_, _01b_, _01c_ and _01d_

**Data upload method:** automated upload to sftp bristol

# Module 01e Overview: Conduct GWAS of genetic PCs
GWAS commonly adjust for population structure by using PCs as covirates. However, [Grinde (2024)](https://journals.plos.org/plosgenetics/article?id=10.1371/journal.pgen.1011242#sec010) indicates that PCs may capture not only global ancestry but also local genomic features, which can introduce collider bias and lead to spurious associations. Excluding known high LD regions and then conducting strict LD pruning before PCA reduces local artifacts in PCs effectively, while excluding high LD regions alone is insufficient.