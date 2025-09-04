## Set up of main environment for DEEP pipeline

The pipeline requires R version 4.1 or higher, python 3 and python 2.7 and several other tools. There are two options to install the necessary dependencies and Rpackages:

#### Option 1: Create and activate conda/mamba environments (recommended) 
[conda/mamba](https://github.com/conda-forge/miniforge?tab=readme-ov-file) environments allow you to manage the environment while running this pipeline eg they ensure that the correct dependency versions are used for the DEEP pipeline scripts. 

For DEEP, we generated two different environments: `deep_env` for most of the QC and analyses and `hase_py2` for the mQTL analyses.
The `deep_env` environment will require ~4.1 GB of storage, and the `hase_py2` will require 588 MB.

Please ensure you have [conda/mamba](https://github.com/conda-forge/miniforge?tab=readme-ov-file) **installed** and **initialised** before running these commands:
```
# Please ensure that no duplicate environment names are used before creating our environment.
# To create the main environment for DEEP pipeline
mamba env create --file deep_env.yml
# To create the python2 environment to run meQTL analysis
mamba env create --file ./resources/bin/hase/environment.yml
```
 If you use conda, please replace `mamba` with `conda` in all commands. Please note that if you have used this option you do not need to specify the Python and R directories of your local environment in your config file.

### After you create your environment by using conda/mamba, these are the packages absent from conda-forge, which you need to install in R.
In the location on your server where you have cloned this repository, you need to run:

```bash
mamba activate deep_mqtl
wget http://homepages.uni-regensburg.de/~wit59712/easyqc/EasyQC_9.2.tar.gz
```

In R, run:
```r
install.packages("parallel")
install.packages("EasyQC_9.2.tar.gz")
```

#### Option 2: If you do not use mamba/conda, then please specify the Python2, Python3, and R directories in your config file. Please ensure you have AT LEAST:

* `R version 4.1`
* `Python3 version 3.9`
* any distribution of `Java 11`

### If you do not use mamba or conda, please install these R packages in your R manually:
```r
install.packages("MASS")
install.packages("lattice")
install.packages("ggplot2")
install.packages("data.table")
install.packages("MatrixEQTL")
install.packages("parallel")
install.packages("matrixStats")
install.packages("remotes")
install.packages("plyr")
install.packages("Cairo")
install.packages("plotrix")
install.packages("EasyQC_9.2.tar.gz")
install.packages("cluster")
install.packages("RPMM")
install.packages("tibble")
install.packages("vioplot")
install.packages("qqman")
install.packages("ggtext")
install.packages("diptest")
install.packages("mixtools")
install.packages("corrplot")
install.packages("janitor")
install.packages("tidyr")
install.packages("dplyr")
install.packages("readr")
install.packages("ggpubr")
install.packages("ggrepel")
install.packages("magrittr")
install.packages("R.utils")
install.packages("BiocManager")
install.packages("DescTools")
```
## Other R packages are required for both mamba users and non-mamba users.
### Please install EpiDISH and DunedinPACE from GitHub and not from Bioconductor.
```r
library(remotes)
library(BiocManager)
Sys.setenv(R_REMOTES_NO_ERRORS_FROM_WARNINGS="true")
BiocManager::install("genefilter")
remotes::install_github("sjczheng/EpiDISH")
remotes::install_github("danbelsky/DunedinPACE")
```

### Please install GwasDataImport to infer genome build
```r
library(BiocManager)
BiocManager::install("rtracklayer")
BiocManager::install("biomaRt")
remotes::install_github("MRCIEU/GwasDataImport")
```

### Please install EWAFF
```r
library(remotes)
Sys.setenv(R_REMOTES_NO_ERRORS_FROM_WARNINGS="true")
remotes::install_github("perishky/ewaff")
```
### Please install scoreInvHap and impute from BiocManager
```r
library(BiocManager)
BiocManager::install("scoreInvHap")
BiocManager::install("impute")
```

### Please install these packages if you have related samples
```r
library(BiocManager)
BiocManager::install("GWASTools")
BiocManager::install("SNPRelate")
BiocManager::install("GENESIS")
```
### Please install meffonym
```r
library(remotes)
install_github("perishky/meffonym")

```
### You need to install meffil. You need to have version 1.3.8 or higher. You can find out the version with the commands below. 
```
library(meffil)
sessionInfo()
```

Firstly install some of the dependencies:
```r
BiocManager::install("SmartSVA")
BiocManager::install("illuminaio")
BiocManager::install("limma")
BiocManager::install("DNAcopy")
BiocManager::install("preprocessCore")

#Then install meffil in R:
library(remotes)
Sys.setenv(R_REMOTES_NO_ERRORS_FROM_WARNINGS="true")
remotes::install_github("perishky/meffil")
```

### You need to test whether the preprocessCore R package multi-threading works
```r
threading.ok <- function() {
	require(preprocessCore)
	y <- matrix(10+rnorm(100),20,5)
	ret <- tryCatch({normalize.quantiles(y); "correct"}, error=function(e) e$message)
	!grepl("pthread_create", ret)
}
threading.ok()
```
If `threading.ok()` returns TRUE, then you shouldn't have a problem. If it returns FALSE, then you'll need to reinstall `preprocessCore` with threading disabled as shown below:

```r
BiocManager::install("preprocessCore", configure.args="--disable-threading", force=TRUE)
```
If you still encounter the error about the `preprocessCore` and under the conda environment, please manually install it by following:
```
git clone https://github.com/bmbolstad/preprocessCore.git
R CMD INSTALL --configure-args="--disable-threading" path/to/preprocessCore
``` 


### You need to test whether R can find the path to your R libraries

You can test this in R:

```r
library(ggplot2)
```

If this doesn't work, then you may need to add the following line to your `.bashrc` file:

Open your `.bashrc`
```
nano ~/.bashrc
```
Add the following line to your `.bashrc` 

```
export R_LIBS="/path/to/library/"
```

Restart the terminal, and any R-script should automatically look for packages in your specified directory. You can test this by opening R and testing a library

```r
library(ggplot2)
```

#### You also need to make sure the scripts in the pipeline can find Rscript

You can test this by typing

`Rscript`

If this doesn't work, then you need to add the following line to your `bashrc` file

```
#You need to edit the line
export PATH="$PATH:/path/to/directory/R-4.1.2/bin/"
```
#### You need to test whether you are able to generate plots in R
Open R and type

```
 capabilities()
```
png should be TRUE

#### You need to make sure you have git installed

```
which git
```

#### You need to make sure you have encryption software installed

We are encrypting and uploading the results with aes encryption using `gpg` and `pinentry`, and `sshpass`. You can test whether the gpg, pinentry, and sshpass Linux packages have been installed with the following command:

```
which sshpass
which gpg
which pinentry
```
If gpg hasn't been installed, then you can install it from here: [https://gnupg.org/download/](https://gnupg.org/download/). You can install pinentry from here: [https://www.gnupg.org/download/index.html#pinentry](https://www.gnupg.org/download/index.html#pinentry). You can install sshpass from here: [https://www.cyberciti.biz/faq/noninteractive-shell-script-ssh-password-provider/](https://www.cyberciti.biz/faq/noninteractive-shell-script-ssh-password-provider/).

 


