# MODULE STATUS

**Developers:** Olalekan Awoniran & Josine Min

**Scripts status:** Ready

**Prerequisite scripts:** _script 00, 01, 02, 03_

**Data upload method:** automated upload to sftp bristol

### Background
A comprehensive catalogue of SNP-methylation associations can be used to understand the molecular determinants of methylation, and for downstream analyses in complex traits, such as Mendelian randomisation, functional annotations etc.

### Inclusion criteria
1. We include all DNA methylation sites covered by the EPIC/450k array. 
2. We will use imputed or sequenced variants (hg19/GRCh37 or hg38/GRCh38).
3. We will use a new methodology, [HASE](https://github.com/roshchupkin/hase), which means we can save and ultimately meta-analyses the full surface of mQTLs.

### Strategy
Performing hundreds of thousands GWASs will result in a huge computational burden if using standard tools, and will also require huge amounts of disk space to store all results. Although software packages such as matrixeQTL/tensorQTL solves the computational burden only [HASE](https://github.com/roshchupkin/hase/wiki) is able to solve the computational burden and storage issues. HASE will be used to perform a fast, comprehensive analysis of all _cis-_ and _trans_-associations on residualised probes. Partial derivatives (we will not exchange private data) will be sent to a central SFTP in Bristol.  Results from this step will be used for the final meta analyses and conditional analyses.

### Setup
We have developed a python 2.7 compatible version of HASE for this project which you can find in the `./resources/bin/hase` folder.
#conda env will be activated in 04a.

### Convert to HASE format
We need to generate the correct format for the MeQTL analysis software (HASE). To convert PLINK to HASE format you need to run the following script from your home directory:



``` 
    ./04a-convert_snp_format.sh
```

This script will copy the PLINK files from script 02 to `/processed_data/hase/hase_in`. It then converts the data into h5 files into "genotype", "individuals" and "probes" directory in `/processed_data/hase/hase_converting`.

### Create mapper files

Next we need to run three scripts to map the converted data to the HRC reference panel. 

First we need to check that the reference panel files `ref-hrc.ref.gz` and `ref-hrc.ref_info.h5` have been downloaded in step 01:

```
ls ./resources/bin/hase/data
```

Then we need to run
```
./04b-mapper-preparation.sh
```
to solve issues with variant names and flipped alleles.

Next we will map the files to the TopMED reference file.
```
./04c-create_mapper_files.sh
```

This will generate the following files:

```
source config
ls -l ${hase_mapping}
```

- `flip_{reference panel name}_{study name}.npy` - info about flipped alleles
- `keys_{reference panel name}_{study name}.npy` - reference variants names
- `values_{reference panel name}_{study name}.npy` -info for HASE about missed variants and order

### Encoding data

So far it has not been possible to run a full surface mQTL meta-analysis across multiple cohorts due to the massive file sizes. Therefore HASE implemented the encode method (HD partial derivatives) to reduce file size. This method does not exchange private data. More details in [HASE paper](https://doi.org/10.1038/srep36076)

```
./04d-encoding.sh
```

HASE will create several directories in `ls ${hase_encoding}`: `encode_genotype`, `encode_individuals`, `encode_phenotype`. Additionally it will save two numpy files `F.npy` and `F_inv.npy` - matrix, which were used to encode data.

### Single site analysis

Single site analysis means computing only partial derivatives for sharing with central site, therefore significantly reduces computational time.

```
./04e-single-site-analysis.sh
```

HASE will create several numpy files in the `${hase_single_site}` folder. These files contain partial derivatives, which will be shared with central site for the meta-analysis.

We will tar the results before uploading.

```
./04f-tar_results.sh
```

### Upload the results

To check that everything ran successfully, please run:

```
./check_upload.sh 04 check
```

This should tell you that `Section 04 has been successfully completed!`. Now please upload the results like this:

```
./check_upload.sh 04 upload
```

It will make sure everything looks correct and connect to the sftp server. It will request your password (this should have been provided to you along with your username). Once you have entered your password it will upload the results files from section 04.

