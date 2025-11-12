# nhanes-depression-analytics

## 📂 NHANES Datasets Used (2017–2018)

This project uses publicly available data from the **National Health and Nutrition Examination Survey (NHANES)** conducted by the **Centers for Disease Control and Prevention (CDC)**.  
The analysis focuses on the **2017–2018** cycle, using mental-health, laboratory, examination, and demographic components.

All datasets are merged by the participant identifier variable **`SEQN`**.

| **Category** | **Dataset Name** | **Data File Name** | **Key Variables** | **Explanation of What Each Variable Represents** |
|--------------|------------------|---------------|-------------------|------------------------------------------------|
| **Questionnaire** | *Mental Health – Depression Screener* | `DPQ_J.XPT` | `DPQ010`–`DPQ090`, `DPQ100` | Patient Health Questionnaire-9 (PHQ-9) items assessing depressive symptoms such as low interest, sadness, fatigue, sleep disturbance, appetite change, poor concentration, movement changes, and self-harm thoughts. `DPQ100` captures how difficult these problems made daily life. |
| **Laboratory** | *High-Sensitivity C-Reactive Protein* | `HSCRP_J.XPT` | `LBXHSCRP`, `LBDHRPLC` | `LBXHSCRP`: serum CRP (mg/L), an inflammation biomarker. `LBDHRPLC`: comment code noting results below detection limits or quality flags. |
|  | *Insulin* | `INS_J.XPT` | `LBXIN`, `LBDINSI`, `LBDINLC` | `LBXIN`: fasting insulin (µU/mL). `LBDINSI`: SI unit conversion (pmol/L). `LBDINLC`: comment code for low or invalid readings. Analyses using fasting insulin must apply **`WTSAF2YR`** from `DEMO_J` for weighting. |
|  | *Plasma Fasting Glucose* | `GLU_J.XPT` | `LBXGLU`, `LBDGLUSI` | `LBXGLU`: fasting plasma glucose (mg/dL). `LBDGLUSI`: SI units (mmol/L). Analyses using fasting glucose must apply **`WTSAF2YR`** from `DEMO_J`. |
| **Laboratory** | *Complete Blood Count* | `CBC_J.XPT` | `LBXWBCSI`, `LBDLYMNO`, `LBDMONO`, `LBDNENO`, `LBDEONO`, `LBDBANO`, `LBXRBCSI`, `LBXHGB`, `LBXHCT`, `LBXPLTSI` | CBC provides comprehensive blood cell and hemoglobin metrics: white blood cells, lymphocytes, monocytes, neutrophils, eosinophils, basophils, red blood cells, hemoglobin, hematocrit, and platelets — indicators of immune, oxygen-carrying, and clotting functions. |
| **Examination** | *Body Measures* | `BMX_J.XPT` | `BMXWT`, `BMXHT`, `BMXBMI`, `BMXWAIST`, `BMXHIP` | Anthropometric measures including weight (kg), height (cm), BMI, waist, and hip circumference. Used to compute **waist-to-hip ratio** (`BMXWAIST / BMXHIP`) for body-composition analysis. |
| **Demographics** | *Demographic Variables* | `DEMO_J.XPT` | `SEQN`, `RIDAGEYR`, `RIAGENDR`, `RIDRETH3`, `DMDEDUC2`, `DMDMARTL`, `DMDHHSIZ`, `INDFMPIR` | Provides participant demographic and socioeconomic background: age, sex, race/ethnicity, education level, marital status, household size, and family income-to-poverty ratio. |

---

### 🔹 Data Source
All datasets were downloaded from the official NHANES data portal:  
👉 [https://wwwn.cdc.gov/nchs/nhanes/](https://wwwn.cdc.gov/nchs/nhanes/continuousnhanes/default.aspx?BeginYear=2017))

### 🔹 Notes
- The **`SEQN`** variable links all components for each participant.  
- Only key analytical variables are shown; other helper/ quality control fields were reviewed but excluded for making this file concise.  
- Weight selection rule of thumb:  
  - Use **`WTINT2YR`** for interview-only variables (e.g., questionnaires).  
  - Use **`WTMEC2YR`** for MEC/exam variables (e.g., body measures).  
  - Use **`WTSAF2YR`** for fasting subsample lab analyses (e.g., insulin, glucose).  
- Together, these datasets allow integrated study of **mental health (PHQ-9)**, **biomarkers (CRP, glucose, insulin, CBC)**, **physical health (BMI, waist-to-hip ratio)**, and **socioeconomic context**.

## Notebook: Rawfiles_Conversion_to_CSV.ipynb
This notebook contains the code shows all .csv files converted from NHANES .xpt data files in a single directory for the 2017–2018 cycle in less time instead of manually converting one after another.

## Notebook: 01_explore_phq9.ipynb
**Objective:**

Load and inspect the PHQ-9 depression screener data (DPQ_J.XPT / DPQ_J.csv) from the NHANES 2017–2018 cycle to determine its quality and structure before analysis.

**Tasks Performed:**

•	Load the dataset with pandas.

•	Print .shape, .head(), .dtypes, and missing-value counts.

•	Verify variable names match PHQ-9 items (DPQ010–DPQ100).

•	Summarize observations in Markdown, including data types, completeness, and any problems.

**Expected Outcome:**

A cleaned dataset where each record is a participant and each column is a PHQ-9 survey question. This step ensures data consistency and readiness for merging with demographic data.

## Notebook: 02_merge_demo_depression.ipynb
**Objective:**

• Combine the NHANES Demographics data set (DEMO_J.XPT / DEMO_J.csv) and the Depression Screener data set (DPQ_J.XPT / DPQ_J.csv) using the common participant ID SEQN.

• This combination adds background demographic information (age, sex, race, education, marital status, income) to mental health responses, creating one data set for analysis.

**Tasks Performed:**

• Loaded both data sets (DEMO_J and DPQ_J) with pandas.

• Replaced small floating-point artifacts, such as 5.39e-79, with 0 values.

• Verified that updated counts matched the official NHANES DPQ_J codebook exactly.

• Ordered value counts (0, 1, 2, 3, 7, 9, NaN) in a sequence that corresponds with the codebook to maintain consistency of representation across all PHQ-9 items.

• Counted the number of responses that were coded as 7 (“Refused”) and 9 (“Don’t know”) for each PHQ-9 item.

• Calculated the total number of unique participants who had at least one skipped/refused response.

• Verified that a small subset of participants, 26 in total, had 61 non-responses across the items.

• Recoded 7 and 9 into NaN in order to handle them as missing values in subsequent analyses.

• Ensured accuracy and completeness of data before merging with DEMO_J (demographic data).

• Verified for uniqueness of SEQN in every dataset to ensure that it can be utilized as an effective merge key.

• Executed an inner join based on SEQN, keeping only the participants that occur in both datasets.

• Compared dataset shapes before and after merging to ensure merging was successful.

• Reviewed merged data preview for successful integration of variables.

**Outcome:**

• A merged dataset containing 5,533 participants who have both demographic and PHQ-9 depression data and 56 variables with SEQN in common.

• The merged file now includes core demographic variables (age, sex, race, education, marital status, household size, income-to-poverty ratio) alongside PHQ-9 survey responses, ready for further statistical modeling or exploratory analysis.

• This result aligns with NHANES design, where only a subset of respondents complete the mental-health screener.

## Notebook: 03_depression_outcome.ipynb
**Objective:**

• To calculate PHQ-9 depression outcomes using the merged and cleaned dataset from Notebook 02.

• This notebook computes the total PHQ-9 scores for each respondent, determines probable cases of depression (PHQ-9 ≥ 10), and summarizes overall prevalence and data completeness for further analysis.

**Tasks Performed:**

• Imported the merged dataset, which was created in Notebook 02: merged_DPQJ_DEMOJ_cleaned.csv.

• Calculated the number of PHQ-9 items answered and each participant's total PHQ-9 score.

• Generated descriptive statistics-count, mean, standard deviation, min, max, quartiles-to assess the distribution of scores.

• Created a binary variable for probable depression using PHQ-9 ≥ 10.

• Calculated the percentage of participants meeting the PHQ-9 ≥ 10 criterion.

• Exported the preprocessed dataset to data_processed/merged_with_phq9_scores.csv.

• Wrote an interpretation summary that described participant counts, prevalence, and data quality.

**Expected Outcome:**

• A cleaned dataset that contains:

• Total PHQ-9 score for each participant and number of items answered. A binary variable indicating probable depression (PHQ-9 ≥ 10). 

• Summary statistics and prevalence estimates for depression in the NHANES 2017–2018 sample. 

• This dataset will serve as the input for further statistical analysis in the next stage of the project.

