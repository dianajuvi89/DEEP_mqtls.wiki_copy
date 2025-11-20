Welcome to the [Diverse Epigenetic Epidemiology Partnership](https://www.deep-epigenetics.org/) analysis pipeline. This wiki will guide you through the different stages of the analysis. We recommend you read this page before you start running the pipeline to familiarise yourself with how it was designed and what it includes, how to use this wiki, and what to do if it goes wrong.

A reminder of our participation requirements can be found on this [**page**](Participation-requirements).

## Design overview
Each analyst runs the pipeline for their cohort on their own server.

The pipeline was designed to make it as automatic and reproducible as possible, minimising coding and input required by users ("analysts"). This makes the pipeline easy to execute, avoids duplication of effort, maintains consistency across cohorts and reduces potential for errors. 

Analyst input is minimised by using a `config` file. This config file includes all cohort-specific parameters such as file paths, properties of the data, thresholds, etc. 

Pipeline stages are separated into numbered modules. Modules need to be completed in sequence. [This flowchart](Script-Flowchart) represents the dependencies between the modules so you can see what order you need to run them in. 

## Content overview

The pipeline starts with a number of sequential modules that ensure you have the correct software requirements, genetic data, DNA methylation data, phenotype data and covariate data. This generates a number of outputs that analysis modules can use later in the pipeline.

The analyses within this pipeline are:

### Perform a mQTL analysis

Perform a full GWAS on every methylation probe available in the sample. Variations of this analysis will be performed, including:

- a standard mQTL analysis using bestguess imputed SNPs covering the full surface using new software
- cell-type interacting mQTL analyses for abundant cell types
- a var-mQTL analysis which looks for SNPs that influence the variance of a probe.

### EWAS of cardiometabolic traits

We will be conducting EWASs on a range of complex traits:
- Prevalent Type 2 Diabetes
- Body Mass Index
- Blood Pressure
- Blood glucose
- HbA1c
- Blood lipids
- Smoking (y/n)
- Alcohol intake

### EWAS of child health phenotypes

- Child height
- Child lipids
- Mothers smoking

We will conduct EWASs using the following models:

- standard EWAS
- Variance EWAS
- Cell type interaction EWAS
- DMR analyses

Please note, not all of these modules are ready to be run. Those that are have been included in this repository on the main branch. The others are being developed and will be released once they are ready. You will be notified when new analyses are ready for you to run. 

## How to use the wiki

This wiki is structured to guide you through each stage of the analysis. There is one page per module. At the top of each page is a standard header which lets you know the status of each module (whether it is ready to be run or not), which scripts need to be completed before running this module, and instructions for uploading results at the end. 

After this, the page will describe the analysis performed in the module and it will give example commands to implement the script. 

## What to do if it goes wrong

If you encounter any unexpected behaviour when you follow the instructions within this wiki please report this to us. We would like all communication about the pipeline to go through GitHub and this repository. 

There are two mechanisms for sharing any errors or comments.

1. [Issues](https://github.com/MRCIEU/DEEP_mqtls/issues). This is the mechanism to report bugs or errors with the pipeline or documentation. Essentially anything which requires us to make edits to the content in the repository. There are more guidelines on how to submit an issue below.
2. [Discussions](https://github.com/MRCIEU/DEEP_mqtls/discussions). This is the place to ask questions or make suggestions about the pipeline and analyses. 

## Submitting an issue

Before you submit an issue, please take a moment to review these guidelines to ensure your issue is addressed effectively.

### General Guidelines

**Search Existing Issues:** Before submitting a new issue, please search the existing issues (including closed issues) to see if your problem has already been reported. This helps avoid duplicates and allows us to focus on a single thread of discussion for each issue.

**Use Clear and Descriptive Titles:** Provide a concise summary of the issue in the title. This helps others understand and search for similar issues.

**Provide Detailed Information:**

For Bugs: Tell us which script you were running when the error occurred, the expected result, and the actual result. Provide any relevant logs or screenshots.
For Documentation: Specify the page & section where the documentation was misleading or confusing. 

Thank you for reading this guide, and for your partnership in the DEEP study. You are ready to get started!

