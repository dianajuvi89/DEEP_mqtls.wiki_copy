### Content of the repository:

Using the instructions in System requirements you have downloaded the scripts. The location of this repository will be your `scripts_directory` variable that needs to be included in the config file below. In this directory you will find the following:
 
**You do not need to manually change any of these files**.
- `config.example` file: Your first task in setting up the pipeline is to create a `config` file. The `config.example` is a template for the config file that the pipeline relies on to specify your data and parameters. You will need to customise this file to point to your input data files. Details for how to create this file are found below.
- `*.sh` files: These are the scripts that you need to run in sequence to perform the analysis. They will be explained step-by-step on the [wiki](https://github.com/MRCIEU/DEEP_mqtls/wiki). 
- `LICENSE`: Details what you can and can't do with the code in this repository.
- `README.md`: Overview of contents of repository.
- `resources` directory: This contains a number of internal scripts, executables, and support files that will be used by the various `.sh` scripts to run the analyses. 
**You do not need to manually change any of these files**.

## Setup your config file

The first thing to do is to create your `config` file. A template is provided in the repository in the `config.example` file. Your config file **should not** be created by editing this template directly. Instead you should make a copy of it for example as follows:

```
cp config.example config
```

You can now modify the `config` file (**not** the `config.example` file!) to specify the location of your input data, software and the parameters that will be used for the analysis.

Open the file in a text editor, and change the entries for the following variables:

- `study_name`: Please make sure this is alphanumeric (underscores allowed; no spaces allowed). 

- `analyst_name`: Analyst's name

- `analyst_email`: Analyst's email address

- `sftp_username`: This should be provided to you from the DEEP PIs (along with a password) after registration (see "Upload of results for meta-analysis section" below).

- `key`: see "Upload of results for meta-analysis section" below on how to set up your key.
 
- `home_directory`: This directory contains a folder called `input_data` where the genetic, methylation and covariate files are located. Within this folder the pipeline will create a `processed_data` and `results` folder for the intermediate and results files.  Please make sure you have enough space in this directory for all the output files and the correct permissions to create new files and folders.

- `scripts_directory`: If you are in the `DEEP_mqtls` folder then type `pwd` and set this variable to the path: eg `scripts_directory="path/to/DEEP_mqtls"`.

- `R_directory`: Full path to the R directory if you don't use conda/mamba. Otherwise, leave blank. 

- `Python_directory`: Full path to the Python3 directory if you don't use conda/mamba. Otherwise, leave blank. You need Python 3.9 or later for Hail package.

- `Python2_directory`: Full path to the Python2 directory if you don't use conda/mamba. Otherwise, leave blank. You need Python 2.7 for hase.

- `methylation_array`: One of the following: 450k, epic, epic2 or epic:epic2. Please note that names are case-sensitive.

- `tissue`: One of the following: blood, PBMC, PBL, cord, saliva, or buccal

- `study_specific_vars`: Names of study specific variables for EWAS. These need to be the column names for these variables and so will need _factor or _numeric at the end. Please leave a space between each variable "slide_factor plate_factor"

- `genome_build`: Please specify the genome build of the genetic data ("37" or "38").

- `sorted_methylation`: Please specify whether methylation has been obtained for sorted cell types or bulk tissue (yes if you have e.g DNA methylation from sorted cell types. no if you don't).

- `age`: Age at methylation measurement. Please specify one of the following: "<18", ">18", "all".

- `relatedness`: Specify whether there is relatedness in your samples ("yes" or "no").

- `reference`: Please specify reference panel ("1000g", "1000g+h3a", "1000g+hrc", "topmed", "1000g+caapa", "hrc"). This is case-sensitive.

- `ancestry`: Specify the most likely genetic ancestry of your samples with one of the following: "EUR", "AFR", "AMR", "EAS", "SAS", or "None" if you are not sure, or ancestry is not matching with any of these populations. This step is required by EasyQC, to compare the allele frequencies of your samples against reference panel of 1000G populations.


## Preparing data for the pipeline


### Input data

You need to generate a folder called `input_data` in where the pipeline will look for the required files. The folder that contains this folder, will be the `home_directory` variable you specify in the config file. If you have this variable loaded in a unix session you can create it by running 

 ```
 mkdir -p ${home_directory}/input_data
 ``` 
 
Inside this folder you need to ensure that there is:

- Genetic data in binary plink format (i.e. `bed`, `bim` and `fam`) files without a genotype probability cut-off. Please see [here](Imputed-genetic-data) for more info.
- Normalised and QC'd methylation data stored in an `.RData` file. Please see [here](Normalised-methylation-data) for more info.
- Covariates stored as plain text files. Please see [here](Covariate-data) for more info.

For simplicity you can copy or move your genetic data (e.g. `data.bed`, `data.bim`, `data.fam`), your methylation data (e.g. `beta.RData`), and your covariate data (e.g. `covariates.txt`) to the `input_data` directory. Then set the following variables in the config file:

```
betas="${home_directory}/input_data/beta.RData"
bfile_raw="${home_directory}/input_data/data"
covariates="${home_directory}/input_data/covariates.txt"
```

#### Imputation quality

It's important to check that the imputation quality is as expected. Remember: we are expecting best guess genotypes that have been filtered on MAF > 0.01 and imputation quality > 0.8. Please provide a file that details the MAF and info scores of each SNP included in the analysis. It should look like this:

```
rs1 0.21 0.91
rs2 0.42 0.95
rs3 0.23 0.81
```

where the first column is the SNP identifier (that matches the SNP identifiers in the `bim` file), the second column is the MAF and the third column is the info score. Details on how to generate this file are provided [here](Imputed-genetic-data). Please copy this file to `${home_directory}/input_data` and set the relevant variables in the `config` file, e.g. if the file is called `data.info`.

```
quality_scores="${home_directory}/input_data/data.info"
```


### Cell count data

You do not need to provide cell counts proportions estimated from the DNA methylation data, as they will be predicted by the pipeline using the [Salas reference dataset](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE167998) using the [EpiDISH](https://github.com/sjczheng/EpiDISH) package. If your DNA methylation data were generated from a heterogeneous set of cell types (e.g. whole blood), then please set the following variables in the `config` file:

```
sorted_methylation="no"
measured_cellcounts="NULL"
```

If your DNA methylation data were generated from  a homogeneous cell type then set these to:

```
sorted_methylation="yes"
measured_cellcounts="NULL"
```

If you have empirically measured cell counts available which can be used for comparison to the predicted cell counts for your methylation samples then set these variables to:

```
sorted_methylation="no"
measured_cellcounts="/path/to/measuredcellcounts.txt"
```

You will need to format your measured cell counts as follows:

```
IID mono tcell bcell eos neu baso
1 0.42 0.10 0.20 0.01 0.4 0.01
2 0.23 0.81 0.21 0.02 0.41 0.02
```

You don't need to have all cell counts from the example above in your file.
Please note that we are not including data generated from cord blood samples at this time.

### Relatedness

If you have family relationships in your data (i.e. twin or family study design) then please specify this by setting the parameters in the config file:

```
related="yes"
```

Otherwise set the parameter:

```
related="no"
```
In this case the pipeline will attempt to find any cryptic related individuals and remove them.

### Computation

The pipeline uses `plink2` and `gcta` which both have multi-threading capabilities. It also uses the `R/parallel` package to speed up R calculations. For some of the scripts in the pipeline these multi-threading options will be used. Please specify how many threads you have available with the following variable in the config file (e.g. if you have 16 threads available):

```
nthreads="16"
```

In addition, some of the computationally slower processes can be parallelised across multiple nodes on a cluster. You can modify the following option to customise how many batches to split long running jobs into:

```
meth_chunks="100"
genetic_chunks="100"
```

The default is to split the methylation normalisation routines into batches of 100 (`meth_chunks`), and the genotype based analyses (e.g. mQTL analysis, multivariate LMM of cellcounts) into batches of 100 (`genetic_chunks`). But you can change this to whatever suits your system best. If in doubt leave as the these values to start with. 

### Other variables/parameters

There are other options that are specified in the config file. For most cohorts the default settings should be fine. Please review these and contact us if you have any further questions. 


## What if I have multiple datasets to process?

The pipeline has been designed such that if you have multiple datasets, you only need to maintain one copy of the repository. This is preferable and it means it is easily to keep up to date and saves on space. 

The pipeline assumes by default that your config file is located in your `scripts_directory` so the pipeline can find it easily. However, if you have multiple datasets you will need multiple config files, one per each dataset as well as multiple `home_directory` folders. There is the option to run each script in the pipeline with a custom path to the config file. This means you can maintain multiple config files, one for each dataset, on your system at different file paths (for example within the `home_directory`).

If you want to run a config file that is not located in the `scripts_directory` you add the flag `-c` followed by the path to the config file you want to include on that execution. You need to do this for every script in the pipeline. For example in the setup script described below you can run: 

```
./00-setup_folders.sh -c /path/to/config/file
```

By directly specifing the config file you want to use, you can easily run the same pipeline on different sets of data. 



## Upload of results for meta-analysis

Once modules are complete results will be shared to the relevant people from the core team for meta-analysis. Different core researchers have chosen different ways of doing this. For example they may be uploaded to sFTP servers at the University of Bristol (modules 01-05) or to Google drive.  

**Note: No individual level data will be uploaded by these scripts and results files will be encrypted!** 

We will provide you with an encryption password for each cohort and the relevant login details for each server. 

For some of these servers you need to ensure you have the correct files on your system and share some information to enable this to happen automatically. The following sections detail the steps needed to set these up. 

### Setting up a key for the Bristol sFTP server

You must [register](http://data.bris.ac.uk/collaborator/) as a collaborator before you can upload or download any files to the Bristol sftp server. Please email [Josine Min](mailto:josine.min@bristol.ac.uk) for project id or if you have any issues with setting this up. Please note that **every analyst** will need their own account to access the server. If there are two analysts at the same institution or analysing the same cohort you will need your own log in. Sharing passwords and usernames will not work due to the need to have an ssh key pair (see below). 

To upload or download files from the Bristol server to your server/HPC you need to have a key pair on your server.

You may already have a key pair. To check launch a terminal on your HPC and enter the following:

```
ls ~/.ssh
```

The name of a private key is usually of the form `id_` (where type represents the signature algorithm used e.g. dsa or rsa or username). Its public counterpart has the extension `.pub`. The identification file points to the pair in use.
On our system I have the following key:

```
key="~/.ssh/id_rsa"
```

If these files are missing you'll need to create a new pair.

```
ssh-keygen
```

Use the default path and then enter a pass-phrase for your private key. You will be asked for this phrase at points when the key is to be used. This creates a hidden directory containing the keys, which can be copied with the following command and pasted into your profile on the Bristol server: 

```
cat ~/.ssh/id_rsa.pub
```
You need to set the path to the key in your config file
```
key="/user/home/username/.ssh/id_rsa"
```
The next step is to add your public key (eg. `~/.ssh/id_rsa.pub`) to the Bristol sftp server: [https://data.bris.ac.uk/collaborator/accounts/edit](https://data.bris.ac.uk/collaborator/accounts/edit). 

After you have been added to the collaborator space you will receive an email with server details to upload your results. This email will also include upload details for other modules. 

### Process for uploading the results

At the end of each section you can check that things have run completely and correctly by confirming that the expected output is present using the `check_upload.sh` script. Where the results are to be uploaded to an sftp server this script will also automate this process. Results uploaded to Google Drive, will need to be completed by a manual process. The process for each module should be clearly stated at the top of the wiki page and details provided at the end of the wiki for how at the end. 

The script `check_upload.sh` takes two arguments:

- the section number (e.g. `01`) 
- the action (`check` or `upload`)

For example, if you run

```
./check_upload.sh 01 check
```

The script will check that the log files and results look as expected from `section 01`. If you run

```
./check_upload.sh 01 upload
```

The script will:

- perform the check
- generate an encrypted file called `results/$study_name_01.tgz.aes`
- generate an MD5 checksum file called `results/$study_name_01.md5sum`
- upload the files to the server

It will request your password before uploading the results.