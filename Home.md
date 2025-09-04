Welcome to the [Diverse Epigenetic Epidemiology Partnership](https://www.deep-epigenetics.org/) analysis pipeline. This wiki will guide you through the different stages of the analysis. We recommend you read this page before you start running the pipeline to familarise yourself with how it was designed and what it includes, how to use this wiki and what to do if it goes wrong.

A reminder of our participation requirements can be found on this [**page**](Participation-requirements).

## Design overview

The objective of the pipeline was to make it as automatic and reproducible as possible by minimising the amount of coding and input those using it (referred to as analysts from here onwards). Hopefully this should it ensure it is straight-forward to execute, avoids duplication of effort, maintains consistency across cohorts and reduces potential for errors. While it does require a decent amount of computational time, analyst time should be limited, ultimately making it more accessible and increasing the number of cohorts that are able to contribute results. 

The way we minimise analyst input is to use a config file to prespecify cohort-specific parameters such as file paths, properties of the data, thresholds etc. The stages of the pipeline are separated into numbered modules. While many of the initially modules are sequential and require the prior modules to have been completed. From module 03 onwards, they don't necessarily need to be completed in numerical order. [This flowchart](Script-Flowchart) represents the dependencies between the individuals scripts and shows how the scripts depend upon each other. 

## Content overview

The pipeline starts with a number of modules that ensure you have the correct software requirements, and preprocesses the genetic, DNA methylation and covariate data. This generates a number of outputs that the analysis modules can use to assume a certain format.

The analyses within this pipeline are:

### Perform a mQTL analysis

Perform a full GWAS on every methylation probe available in the sample. Variations of this analysis will be performed, including:

- a standard mQTL analysis using bestguess imputed SNPs covering the full surface using new software
- cell-type interacting mQTL analyses for abundant cell types
- a var-mQTL analysis which looks for SNPs that influence the variance of a probe.

### EWAS of cardiometabolic traits

We will be conducting EWASs on a range of complex traits:
- Prevalent Type2 Diabetes
- Body Mass Index
- Blood Pressure
- Fasting glucose
- HbA1c
- Smoking (y/n)
- Alcohol intake

### EWAS of child health phenotypes

- Child height
- Child lipids
- Mothers smoking

Please note, not all of these modules are ready to be run. Those that are have been included in this repository on the main branch. The others are being developed and will be released once they are ready. You will be notified when new analyses are ready for you to run. 

## How to use the wiki

This wiki is structured to guide you through each stage of the analysis. There is one page per module. At the top of each page is a standard header which lets you know the status of each module (whether it is ready to be run or not), which scripts need to be completed in advance of running this module, and the mechanism for uploading results at the end. 

After this the page will contain details on the nature of the analysis and example commands to guide you through how it should be implemented. At the bottom of each page there will be details on how to share your results with those responsible for completing the meta-analysis of those results. 

## What to do if it goes wrong

If you encounter any unexpected behaviour when you follow the instructions within this wiki please report this to us. We would like all communication about the pipeline to go through GitHub and this repository. If you email any of the developers they will direct you back to this repository. 

There are two mechanisms for communicating with us.

1. [Issues](https://github.com/MRCIEU/DEEP_mqtls/issues). This is the mechanism to report bugs or errors with the pipeline or documentation. Essentially anything which requires us to make edits to the content in the repository. There are more guidelines on how to submit an issue below.
2. [Discussions](https://github.com/MRCIEU/DEEP_mqtls/discussions). This is the place to ask questions about the pipeline and analyses. 

## Submitting an issue

Thank you for helping improve our project! Before you submit an issue, please take a moment to review these guidelines to ensure your issue is addressed effectively.

### General Guidelines

**Search Existing Issues:** Before submitting a new issue, please search the existing issues (including closed issues) to see if your problem has already been reported. This helps avoid duplicates and allows us to focus on a single thread of discussion for each issue.

**Use Clear and Descriptive Titles:** Provide a concise summary of the issue in the title. This helps others understand and search for similar issues.

**Provide Detailed Information:**

For Bugs: Specify which script you were running when the error occured, the expected result, and the actual result. Provide any relevant logs or screenshots.
For Documentation: Specify the page & section where the documentation was misleading or confusing. 

Thank you for reviewing this information. We would like to thank you in advance for participating in these phase 2 analyses. You are ready to get started!

