# COVID-19 Linear Modeling Project

## Overview

Though the Federal COVID-19 Public Health Emergency Declaration has ended, one question remains: why did countries experience widely different COVID-19 death rates?

This project uses linear regression modeling to predict daily COVID-19 deaths two weeks into the future (`new_deaths_smoothed_2wk`) using country-level indicators such as case counts, health infrastructure, chronic disease prevalence, and age demographics. We explore which factors most strongly influence mortality rates and evaluate model performance across countries.

---

## Datasets Used

### 1. Our World in Data - COVID-19 Dataset
- Source: OWID GitHub
- Data includes daily case counts, deaths, vaccinations, ICU capacity, GDP, diabetes rates, and more.
- Loaded via:
```python
pd.read_csv("https://raw.githubusercontent.com/owid/covid-19-data/master/public/data/owid-covid-data.csv")
```

### 2. World Bank Group – Demographic Data (2023 only)
- Selected population variables (e.g., population over age 80)
- Variables used:
  - Population ages 80 and above, male
  - Population ages 80 and above, female
- Downloaded as CSV, cleaned manually to remove license footer.

---

## Data Wrangling Steps

- Filtered COVID dataset to include only:
  - Country-level rows (3-letter ISO codes)
  - Countries with population ≥ 1 million
- Created a new column `new_deaths_smoothed_2wk` by shifting deaths forward by 14 days.
- Pivoted World Bank demographic data to wide format.
- Merged datasets on ISO country code.
- Created `population_80_plus` by summing 80+ male and female values.

---

## Feature Engineering

To improve modeling, we created the following transformed variables:
- `cases_per_million` = new cases per 1M population
- `log_gdp_per_capita` = log-transformed GDP per capita
- `cardiovasc_deaths` = cardiovascular death rate × population

---

## Linear Regression Modeling

We trained 5 models using 2022 data and evaluated them on January–June 2023 data.

| Model     | Predictors                                                       | R²       | RMSE     |
|-----------|------------------------------------------------------------------|----------|----------|
| Model 3   | new_cases_smoothed, diabetes_prevalence, icu_patients           | 0.8842   | 35.25    |
| Model 5   | cases_per_million, log_gdp_per_capita, population_80_plus, icu_patients | 0.8883   | 36.94    |
| Model 2   | new_cases_smoothed, population_80_plus                           | 0.3413   | 116.97   |
| Model 1   | new_cases_smoothed                                               | 0.2737   | 119.76   |
| Model 4   | cases_per_million, population_80_plus, cardiovasc_deaths        | 0.2034   | 110.01   |

---

## Best Model Evaluation

Model 3 (our best model) was further evaluated by computing RMSE per country. The table below shows RMSE values for the 20 most populous countries, revealing how well the model generalizes across different national contexts.

---

## Key Insights

- ICU patient counts and chronic health indicators (e.g., diabetes) are strong predictors of future COVID deaths.
- Elderly population size and GDP are moderately helpful but less predictive than active health system data.
- Countries with consistent reporting and large populations yield more reliable predictions.
- Model 3 achieved the lowest RMSE and high explanatory power with only three key predictors.

---

## How to Run

1. Clone this repository or download the Jupyter notebook (`project.ipynb`)
2. Install required libraries:
```bash
pip install pandas numpy matplotlib seaborn scikit-learn statsmodels
```
3. Run the notebook from top to bottom.
4. Ensure the World Bank CSV file is in the same directory as the notebook.

---
