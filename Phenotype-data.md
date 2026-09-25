## Phenotypes
This file is used for running DEEP EWAS. Your phenotypes file should be a text file (white space separated) with the sample ID labeled as `IID` in the first column followed by headed columns for each subsequent covariate. In the column name you need to specify whether the covariate is a categorical (`factor`) or continuous (`numeric`) variable. For example: `{Name}_factor` or `{Name}_numeric`.  

>[!IMPORTANT]
> **Variable coding**
> Please check the 'Variable coding' columns below, and ensure your data is coded in this way. Some variables may not be coded as they currently are in your dataset, as we have had to harmonise these variables across the DEEP consortium. This means you may have to recode some of the phenotypes. If you are uncertain about the best way to do so, please get in contact with the DEEP study team.


An example of what your file should look like is below:
```
    IID Sex_factor Age_numeric Slide_factor T2D_factor BMI_numeric SBP_numeric DBP_numeric FGlucose_numeric HbA1c_numeric HDL_numeric LDL_numeric TG_numeric Smoking_factor Smoking_numeric Height_numeric Maternal_smoking_factor Maternal_smoking_numeric
    id1 F 30 12345678 no 23 120 80 3.0 2.0 / 1.2-1.5 1.2 1.8/ no 0 162 yes 20
    id2 M 42 12345678 no 26 130 76 4.0 2.0 1.3 1.3 1.9 yes no 0 173 no 0
    id3 M 76 87654321 yes 32 139 68 1.0 3.0 2.5 no 0 156 no 0
```

### Required

The following covariates are **required** - these covariates are the minimum criteria for contributing to DEEP:

| Phenotype | Variable coding | Variable name |
| --- | --- | --- |
| Age at time of blood sample	| Numeric (years)	| `Age_numeric` |
| Sex	| categorical, F=female, M=male | `Sex_factor` |

Please ensure there are no missing values in the `Sex` and `Age` columns, even if your data is all the same age or all the same sex. Please also ensure that the capitalisation of column headers in the example above is followed.

### Optional

The following variables are required to run EWAS. They are not essential variables, please include them if you have these measures in your study. Please ensure their column headings exactly match those below, including any capitalisation:

### All studies:
| Phenotype | Variable coding | Variable name |
| --- | --- | --- |
| Study specific variables | Categorical (study variables you usually adjust for - sample type, array type, case/control status - please add these to `study_specific_vars` in your config file. | `Case_control_factor` / `Sampletype_factor` etc |
| Measured batch variables | Measured technical batch variables slide and plate. We will not include measured batch variables in the model, as we will use SVA to adjust for measured and unmeasured variation; but we will test association between SVs and measured batch variables to ensure we are sufficiently capturing these with SVA  | `Slide_factor` / `Plate_factor` |
| Population group	| Categorical (unique to study) - the population group the participants in your study belong to. This will be study-specific and will represent a concept such as ethnicity, race, caste, tribe, village, or region	| `Population_group_factor` |
| Address or Geographic location or geocode	| Categorical (unique to study)	| `Location_factor` |
| Urban vs. Rural residence 	| Categorical (urban/rural) - one may need to classify towns; some are only one)	| `Urban_rural_factor` |
| Language spoken	| Categorical (unique to study)	| `Language_factor` |
| Socioeconomic status	| This will be a study-specific socioeconomic variable (either most relevant SES variable, or a composite). If this is a numeric variable then please categorise into quintiles.	| `SES_factor` |
| Height	| Numeric (cm)	| `Height_numeric` |
| Weight	| Numeric (kg)	| `Weight_numeric` |
| BMI	| Numeric (kg/m2)	| `BMI_numeric` |
| Systolic Blood Pressure	| Numeric (mmHg)	| `SBP_numeric` |
| Diastolic Blood Pressure	| Numeric (mmHg)	| `DBP_numeric` |
| CRP	| Numeric (mg/L)	| `CRP_numeric` |
| IL6	| Numeric (pg/L)	| `IL6_numeric` |
| CMV (Cytomegalovirus)	| Categorical (0=no, 1=yes)	| `CMV_factor` |
| HSV (Herpes Simplex Virus)	| Categorical (0=no, 1=yes)	| `HSV_factor` |
| HIV (Human Immunideficiency Virus)	| Categorical (0=no, 1=yes)	| `HIV_factor` |
| EBV (Epstein-Barr Virus)	| Categorical (0=no, 1=yes)	| `EBV_factor` |
| VZV (Varicella Zoster Virus)	| Categorical (0=no, 1=yes)	| `VZV_factor` |
| RSV (Respiratory Syncytial Virus)	| Categorical (0=no, 1=yes)	| `RSV_factor` |
| Malaria	| Categorical (0=no, 1=yes)	| `Malaria_factor` |
| Other	infections | Categorical (0=no, 1=yes)	| `*variable_name*_infection_factor` |

### Child studies only:

| Phenotype | Variable coding | Variable name |
| --- | --- | --- |
| Birth weight	| Numeric (grams)	| `BWT_numeric` |
| Gestational age	| Numeric (weeks)	| `Gestage_numeric` |
| Preterm birth status	| Please categorise as 0=not preterm, 1=preterm	| `Preterm_factor` |
| Mode of delivery (Vaginal vs. C-section)	| Please categorise as 0=Vaginal, 1=C-section	| `Delivery_factor` |
| Breastfeeding duration	| Please categorise as 0=no, 1=yes	| `Breastfeeding_factor` |
| Maternal age at birth	| Numeric (years)	| `Maternal_age_numeric` |
| Paternal age at birth	| Numeric (years)	| `Paternal_age_numeric` |
| Cigarette smoking - Maternal	| Please categorise as 0=no, 1=yes	| `Maternal_smoking_factor` |
| Cigarette smoking - Paternal	| Please categorise as 0=no, 1=yes	| `Paternal_smoking_factor` |
| Alcohol - Maternal	| Please categorise as 0=no, 1=yes	| `Maternal_alcohol_factor` |
| Season of conception	| Categorical (0=spring, 1=summer, 2=autumn, 3=winter)	| `SoC_factor` |

### Adult studies only:

| Phenotype | Variable coding | Variable name |
| --- | --- | --- |
| Prevalent Type 2 Diabetes 	| Please categorise as 0=no, 1=yes	| `T2D_factor` |
| Prevalent Hypertension	| Please categorise as 0=no, 1=yes	| `Hypertension_factor` |	
| Diabetes type 2 medication	| Please categorise as 0=no, 1=yes	| `T2D_med_factor` |
| Hypertension medication	| Please categorise as 0=no, 1=yes	| `Hypertension_med_factor` |
| Cigarette smoking - Own - categorical	| Please categorise as 0 = never/former smoker, 1 = current smoker	| `Smoking_factor` |
| Cigarette smoking - Own - numeric	| Numeric measure (pack years or frequency)	| `Smoking_numeric` |
| Alcohol - Own	Categorical | Please categorise as 0=no, 1=yes	| `Alcohol_factor` |
| Alcohol - Own	numeric | Numeric measure (frequency or quantity)	| `Alcohol_numeric` |
| Physical activity levels	| Ordinal categories. Code with 0 as lowest level and increase from there. If you have a numeric measure please divide into quintiles with 0=lowest and 4=highest quintile.	| `Physical_activity_factor` |
| Air pollution	| Numeric. Add each pollution variable separately. 	| `*variable_name*_pollution_numeric` |
| Length of time living at current address	| Numeric (years)	| `Time_at_address_numeric` |
| Fasting glucose	| Numeric (mmol/L)	| `FGlucose_numeric` |
| Non- fasting glucose	| Numeric (mmol/L)	| `NFGlucose_numeric` |
| HbA1c	| Numeric (%)	| `HbA1c_numeric` |
| High Density Lipoprotein (HDL)	| Numeric (mmol/L)	| `HDL_numeric` |
| Low Density Lipoprotein (LDL)	| Numeric (mmol/L)	| `LDL_numeric` |
| Triglycerides	| Numeric (mmol/L)	| `TG_numeric` |
| Total Cholesterol	| Numeric (mmol/L)	| `Total_chol_numeric` |


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

For Saliva or buccal: The pipeline will generate cell counts for saliva- or buccal-derived methylation data using [Middleton reference dataset](https://pubmed.ncbi.nlm.nih.gov/33588693/) (child saliva) or [HEpiDISH](https://github.com/sjczheng/EpiDISH) (adult saliva or buccal).

Estimated cell counts do not need to be provided in your phenotype file, they will be derived within the DEEP pipeline.
