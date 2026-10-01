# Diabetes Risk Factor Analysis: Reproducibility Exercise

## Purpose
A reproducible, documented version of an exploratory diabetes risk-factor analysis, prepared so the data science team at another clinical site can run it independently and reuse it as a template for their own patient data.

## Analysis overview
The notebook loads a diabetes example dataset, validates it, handles physiologically impossible zero values, summarizes patient characteristics, visualizes relationships with the diabetes outcome, computes a correlation matrix, runs inferential tests comparing outcome groups, and formalizes the problem as binary classification (8 predictors, 1 binary outcome). Randomness (sampling, bootstrap) is seeded so results repeat exactly.

## Notebook
- **Google Colab (primary environment):** `https://colab.research.google.com/drive/1z6oHrt8dVNLlNTtS8fETq-EK7cKvbfrM?usp=sharing`
- **Downloaded copy:** `diabetes_risk_factor_analysis.ipynb` in this repository

## Repository contents
| Path | Description |
|---|---|
| `diabetes_risk_factor_analysis.ipynb` | Final notebook |
| `data/Example Dataset_Diabetes.csv` | Example dataset (768 rows, 9 columns) |
| `requirements.txt` | Pinned package versions |
| `README.md` | This file |

## Dataset source and provenance
The file has the structure of the Pima Indians Diabetes Database (originally from the National Institute of Diabetes and Digestive and Kidney Diseases; widely distributed through the UCI Machine Learning Repository and Kaggle): 768 female patients aged 21 or older of Pima Indian heritage. It is a public teaching dataset standing in for site data, not data from any hospital in this health system. The file was provided with the course materials as `Example Dataset_Diabetes.csv`.

## Dataset requirements
A CSV with one row per patient and these columns:

| Column | Description |
|---|---|
| `Pregnancies` | Number of pregnancies (count) |
| `Glucose` | Plasma glucose, 2-hour oral glucose tolerance test (mg/dL) |
| `D_BP` | Diastolic blood pressure (mm Hg) |
| `Skin_Thickness` | Triceps skinfold thickness (mm) |
| `Insulin` | 2-hour serum insulin (µU/mL) |
| `BMI` | Body mass index (kg/m²) |
| `Pedigree` | Diabetes pedigree function (unitless family-history score) |
| `Age` | Age (years) |
| `Outcome` | Diabetes outcome: 0 = no, 1 = yes (268 of 768 rows are 1) |

All columns must be numeric, and `Outcome` must contain only 0 and 1. The notebook's validation step stops with a clear error if these requirements are not met.

**To use your own site's data:**
1. Format your CSV with the column names and units above.
2. In the Data Source section of the notebook, set `USER_DATA_PATH` to your file's path (in Colab, upload it to the Files panel and use `"/content/<your_file>.csv"`).
3. Update `EXPECTED_ROWS` in the Data Validation cell, and review `BOUNDS` in the Data Preparation cell for your population (for example, the minimum `Age`).
4. Confirm whether your data also record missing measurements as 0; if not, adjust `ZERO_IS_MISSING`.

**Never commit protected health information to a public repository.**

## Required software and libraries 
Verified on Google Colab on September 30, 2026.

| Package | Version |
|---|---|
| Python | 3.13.15 (3.11 or newer required) |
| pandas | 2.2.3 |
| numpy | 2.1.3 |
| matplotlib | 3.10.0 |
| seaborn | 0.13.2 |
| scipy | 1.16.3 |

Libraries: pandas and numpy (data handling), matplotlib and seaborn (figures), scipy (statistical tests). Running locally also requires Jupyter (`notebook`, `ipykernel`), which `requirements.txt` installs. All libraries are preinstalled in Google Colab. All versions are pinned in `requirements.txt`.

## Setup and running

### Option A: Google Colab (recommended)
1. Open the Colab link above.
2. Choose **Runtime > Restart session and run all**.
3. The first code cell prints the Python and package versions in use. Compare them with the Software and versions table above.
4. If any version differs, run the following in a new cell, then choose **Runtime > Restart session** and run all cells again:
   ```python
   !pip install -r https://raw.githubusercontent.com/JillianMelbourne/Diabetes-Risk-Reproducibility/main/requirements.txt
   ```

### Option B: Run locally
```bash
git clone https://github.com/JillianMelbourne/Diabetes-Risk-Reproducibility.git
cd Diabetes-Risk-Reproducibility

python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt

jupyter notebook diabetes_risk_factor_analysis.ipynb
```
In the notebook, choose **Kernel > Restart & Run All**. The notebook reads the dataset from the repository's `data/` folder, so launch it from the repository root.

### Running from start to finish
- Always run the full notebook top to bottom (**Restart session and run all**). Later cells depend on variables created earlier (for example, the cleaned `df`), so running cells individually or out of order can give errors or different results.
- No manual steps are needed for the example dataset. The data load automatically from the repository, or from GitHub if no local copy is found (this requires an internet connection).
- To confirm reproducibility, run the notebook twice and check that the outputs listed under Expected outputs are identical.


## Expected outputs
Expected output for example dataset:
- **Validation:** "All required checks passed: 768 rows x 9 columns; Outcome 0 = 500, Outcome 1 = 268", with zero counts of Glucose 5, D_BP 35, Skin_Thickness 227, Insulin 374, BMI 11.
- **Preparation:** impossible zeros and one 99 mm skinfold value set to missing; 392 of 768 patients have complete data.
- **Descriptive statistics:** n, mean, SD, and median by outcome, plus Cohen's d (largest for Glucose, d = 1.19).
- **Figures:** Glucose and BMI histograms, Glucose by outcome (box plot), BMI vs. Glucose scatter, Spearman correlation heatmap.
- **Inferential tests:** Welch's t-test for Glucose (mean difference 31.7 mg/dL, 95% CI 27.5 to 35.9) and Mann-Whitney U for BMI (median 34.3 vs. 30.1 kg/m², probability of superiority 0.69).
- **Reproducibility check:** bootstrap mean BMI 32.44 (95% CI 31.97 to 32.90).

Running the notebook twice gives identical numbers because every random step is seeded with `RANDOM_SEED = 42`.

## Assumptions and limitations
- In this dataset, zeros in `Glucose`, `D_BP`, `Skin_Thickness`, `Insulin`, and `BMI` are impossible values that encode missing data (counts of zeros: 5, 35, 227, 374, and 11 of 768 rows). The notebook converts them to missing before analysis.
- Values outside clinically plausible ranges (`BOUNDS` in the notebook) are also set to missing; in the example data this removes one triceps skinfold value of 99 mm. These ranges reflect analyst judgment and can be adjusted.
- Missing values are not imputed, and incomplete rows are not dropped. Each analysis uses all patients with the values it needs, so sample sizes differ by analysis (for example, about 393 patients for analyses involving `Insulin`). Only 392 of 768 patients have all eight measurements.
- Missingness is likely not random (missing values tend to occur together in the same patients), so results for `Insulin` and `Skin_Thickness` may not represent the full cohort.
- One population (Pima women aged 21+), so findings may not generalize to other sites' patients.
- Cross-sectional data: associations are not causal.
- The notebook formalizes a prediction problem but does not train or evaluate a model.

## Computational environment
Verified on Google Colab (Linux-6.6.122+-x86_64-with-glibc2.39, Python 3.13.15, standard CPU runtime, no GPU required) on September 30, 2026. The full notebook runs in under a minute. Local execution uses the same pinned package versions via `requirements.txt`; the reference results above were verified in Colab.
