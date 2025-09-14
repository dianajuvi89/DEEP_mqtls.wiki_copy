# MODULE STATUS

**Developer:** 

**Scripts status:** In development

**Prerequisite scripts:** _scripts 00, 01, 02a-02b, 03a-03g, 04_

**Data upload method:** automated upload to sftp to Bristol

## Background
Gene-gene (GxG) and gene-environment (GxE) interactions lead to the increase of DNA methylation variance. A comprehensive search of genome-wide GxG or GxE interaction effects is impractical. Detecting genetic effects associated with DNA methylation variance (variance meQTL or vmeQTLs) is an alternative and efficient way to filter out candidate SNPs, which are likely to be involved in GxG or GxE interactions. See [Yang et al (2012)](https://www.nature.com/articles/nature11401).

## Strategy
**vmeQTL detection will be a two-phase analysis, with a focus on 450k/EPIC methylation data and autosomal chromosome (module 05 scripts will ignore chr 23 as the default). Cohorts participated in vmeQTL analysis should be involved in both phases**.

In phase 1, we will use [BF(Levene's test with median value)](https://en.wikipedia.org/wiki/Brown%E2%80%93Forsythe_test), [DRM](https://eur03.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fdrewmard%2FDRM&data=05%7C01%7Cxiaopu.1.zhang%40kcl.ac.uk%7C3ef85e0abbc64236bbac08da9cadc98e%7C8370cf1416f34c16b83c724071654356%7C0%7C0%7C637994567468218223%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=7XO3UZ9c7nEdDip%2FHc22%2B45qdMfpjxbQjg8vIxUmWtM%3D&reserved=0) and [SVLM](https://eur03.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbmcgenomdata.biomedcentral.com%2Farticles%2F10.1186%2F1471-2156-13-4&data=05%7C01%7Cxiaopu.1.zhang%40kcl.ac.uk%7Cbd04993826454f90411508dad943b412%7C8370cf1416f34c16b83c724071654356%7C0%7C0%7C638061182047507441%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=ZqBPv3C6W%2BJWy6rlArz1tgWcmnQp4ulowcwopviRlKI%3D&reserved=0) tests to detect vmeQTLs. These methods were selected from performance comparison on simulated data (see [Zhang & Bell (2024)](https://academic.oup.com/g3journal/advance-article/doi/10.1093/g3journal/jkae022/7593033)). The current 05 module  only releases the code for the first phase. 

In phase 2, we will send a list of SNP-CpG pairs and scripts to each cohort (scripts are in development).
