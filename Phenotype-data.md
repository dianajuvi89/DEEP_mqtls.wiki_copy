## Phenotypes
This file is used for running DEEP EWAS. Your phenotypes file should be a text file (white space separated) with the sample ID labeled as `IID` in the first column followed by headed columns for each subsequent covariate. In the column name you need to specify whether the covariate is a categorical (`factor`) or continuous (`numeric`) variable. For example: `{Name}_factor` or `{Name}_numeric`.  

An example of what your file should look like is below:
```
    IID Sex_factor Age_numeric Slide_factor PrevalentT2D_factor BMI_numeric SysBloodPressure_numeric DiaBloodPressure_numeric FastingGlucose_numeric HbA1c_numeric HDL_numeric LDL_numeric TG_numeric Smoking_factor Smoking_numeric Height_numeric MaternalSmoking_factor MaternalSmoking_numeric
    id1 F 30 12345678 no 23 120 80 3.0 2.0 / 1.2-1.5 1.2 1.8/ no 0 162 yes 20
    id2 M 42 12345678 no 26 130 76 4.0 2.0 1.3 1.3 1.9 yes no 0 173 no 0
    id3 M 76 87654321 yes 32 139 68 1.0 3.0 2.5 no 0 156 no 0
```

### Required

The following covariates are **required** - these covariates are the minimum criteria for contributing to DEEP:

- `Sex_factor`: A column of `M`'s for males and `F`'s for females
- `Age_numeric`: In years (can be integer or with decimals)

Please ensure there are no missing values in the `Sex` and `Age` columns, even if your data is all the same age or all the same sex. Please also ensure that the capitalisation of column headers in the example above is followed.

### Optional

The following variables are required to run EWAS. They are not essential variables:

- `Slide_factor`: The slide/chip/sentrix ID from the DNA methylation array
-  Any other important batch covariates important for your cohort (list in `study_specific_vars` in your config file)
- `PrevalentT2D_factor`: Coded as `Yes` for prevalent T2D and `No` for controls
- `BMI_numeric`: In Kg/m2 
- `SysBloodPressure_numeric`: In mmHg 
- `DiaBloodPressure_numeric`: In mmHg
- `FastingGlucose_numeric`: In mmol/L
- `HbA1c_numeric`: In mmol/mol
- `HDL_numeric`: In mmol/L
- `LDL_numeric`: In mmol/L
- `TG_numeric`: In mmol/L
- `Smoking_factor`: Coded as `Current` for current smokers and `Former` for ex smokers and `Never` for never smokers
- `Smoking_numeric`: In pack years (can be integer or with decimals)
- `Height_numeric`: In cm 
- `MaternalSmoking_factor`: Coded as `Current` for current smokers and `Former` for ex smokers and `Never` for never smokers 
- `MaternalSmoking_numeric`: In pack years (can be integer or with decimals)

### Not needed

The pipeline will calculate genetic and DNA methylation principal components, so you don’t need to add these to the covariates file. Please note that you shouldn't include any other surrogate variables (e.g. from combat) as these will capture genetic and/or phenotypic variation that we are interested in. 


## Cell counts

We use cell counts as covariates in many of the analyses, interaction terms in the cell type interacting EWAS analysis. If you have directly measured cell counts, then we will use these measured cell counts to compare against predicted cell counts.

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

For Saliva or buccal: The pipeline will generate cell counts for saliva or buccal derived methylation data using [Middleton reference dataset](https://pubmed.ncbi.nlm.nih.gov/33588693/) (child saliva) or [HEpiDISH](https://github.com/sjczheng/EpiDISH) (adult saliva or buccal)

Estimated cell counts do not need to be provided in your phenotype file, they will be derived within the DEEP pipeline.
