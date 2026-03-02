## mQTL covariates
Your covariates file will be used for mQTL analyses. \
Your covariates file should be a text file (white space separated) with the sample ID labeled as `IID` in the first column, followed by headed columns for each subsequent covariate. In the column name, you need to specify whether the covariate is a categorical (`factor`) or continuous (`numeric`) variable. For example: `{Name}_factor` or `{Name}_numeric`.  

An example of what your file should look like is below:
```
    IID Sex_factor Age_numeric Plate_factor
    id1 F 30 12345678
    id2 M 42 12345678
    id3 M 76 87654321
```

### Required

The following covariates are **required** - these covariates are the minimum criteria for contributing to DEEP:

- `Sex_factor`: A column of `M`'s for males and `F`'s for females
- `Age_numeric`: In years (can be an integer or with decimals)

Please ensure there are no missing values in the `Sex` and `Age` columns, even if your data is all the same age or all the same sex. Please also ensure that the capitalisation of column headers in the example above is followed.

### Optional

The following covariates are strongly recommended (but not essential):

- `Plate_factor`: The plate ID from the DNA methylation array
- Any other important batch covariates

### Not needed

The pipeline will calculate genetic and DNA methylation principal components, so you don’t need to add these to the covariates file. Please note that you shouldn't include any other surrogate variables (e.g. from combat) as these will capture genetic and/or phenotypic variation that we are interested in. 


## Cell counts

We use cell counts as covariates in many of the analyses, or as interaction terms in the cell type interacting meQTL analysis. If you have directly measured cell counts, then we will use these measured cell counts to compare against predicted cell counts.

### Directly measured cell counts

If you are providing your directly measured cell count estimates, please make sure you use the same format as the covariate files and save it in a separate file from the covariates file in the input data folder. You should indicate the filename and path in the `config` file. Your header should be the same as in the example below. 

```
IID Bcells Tcells Eos Mono Neu Baso
id1 0.01737535 0.0000000 -3.255819e-19 0.06323692 0.8449611 0.09284286
id2 0.07131901 0.0532302 0.000000e+00 0.05687753 0.7669148 0.07237679
id3 0.16438292 0.1637806 -1.042249e-19 0.11164848 0.3932823 0.25849168
```

### Cell counts derived from DNA methylation data

For blood: The pipeline will generate cell counts for 12 blood cell types from the normalized betas using [EpiDISH](https://github.com/sjczheng/EpiDISH) and the [Salas et al. 2022](https://doi.org/10.1038/s41467-021-27864-7) reference panel.

For Saliva or buccal: The pipeline will generate cell counts for saliva or buccal-derived methylation data using [Middleton reference dataset](https://pubmed.ncbi.nlm.nih.gov/33588693/) (child saliva) or [HEpiDISH](https://github.com/sjczheng/EpiDISH) (adult saliva or buccal)

Estimated cell counts do not need to be provided in your covariate file, they will be derived within the DEEP pipeline.

