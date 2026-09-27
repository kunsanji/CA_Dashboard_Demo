=== SDOH TOOLKIT - PROJECT README ===

Author:            Ba Tung (Jackey) Tran
Institution:       University of Illinois Springfield
Date completed:    2026-09-26
Research question: Which social determinants of health predict county-level
                   diabetes prevalence across California's 58 counties?

--- Analysis configuration ---
State:             ca
Geography:         County level (58 California counties)
Outcome variable:  diabetes_pct
Predictors:        poverty_rate, pct_bachelor_plus, uninsured_rate, pct_lila_tracts
Analysis label:    diabetes_sdoh_model_ca

--- Script run order ---
01_acs.R -> 02_places.R -> 03_svi_eji.R -> 04_hrsa.R
-> 05_usda_nces_chr.R -> 06_merge_all.R -> 07_analysis.R

--- Data sources ---
ACS 5-Year 2020-2024   U.S. Census Bureau, via tidycensus and the Census API
CDC PLACES 2025        Centers for Disease Control and Prevention
SVI 2022               CDC / ATSDR Social Vulnerability Index
EJI 2024               CDC / ATSDR Environmental Justice Index
AHRF 2024-2025         HRSA, Bureau of Health Workforce
Food Access Atlas 2019 USDA Economic Research Service
CHR&R 2025             University of Wisconsin Population Health Institute
CCD 2024-2025          NCES, U.S. Department of Education

--- Data download dates ---
ACS 2020-2024:     Aug 16, 2026
CDC PLACES 2025:   Aug 16, 2026
SVI 2022:          Aug 16, 2026
EJI 2024:          Aug 16, 2026
HRSA AHRF 2024-25: Aug 16, 2026
USDA Atlas 2019:   Aug 16, 2026
CHR&R 2025:        Aug 16, 2026
NCES CCD 2024-25:  Aug 16, 2026

Note: raw source files are not included in this repository because several
exceed GitHub's file size limit. All are free public downloads. See the
header of each script for the exact download location and file name.

--- Output files ---
Master CSV:      data/master/sdoh_ca_master.csv
Tableau CSV:     output/tableau/sdoh_ca_tableau.csv
Health centers:  output/tableau/ca_health_centers.csv
HPSA long:       output/tableau/hpsa_long.csv
Dictionary:      dictionary/data_dictionary.csv
Descriptive:     output/tables/01_descriptive_diabetes_sdoh_model_ca.csv
LM results:      output/tables/03_lm_results_diabetes_sdoh_model_ca.csv
Logit results:   output/tables/04_logit_OR_diabetes_sdoh_model_ca.csv
Correlation:     output/figures/02_correlation_diabetes_sdoh_model_ca.png
Diagnostics:     output/figures/03_lm_diagnostics_diabetes_sdoh_model_ca.png

--- Key results ---
Outcome range: 9.3% (Yolo County) to 15.9% (Trinity County), median 12.0%.

Multiple linear regression, n = 58
  R2 = 0.547, adjusted R2 = 0.513, F(4, 53) = 15.98, p < 0.001

  pct_bachelor_plus   b = -0.047   95% CI -0.078 to -0.016   p = 0.004
  uninsured_rate      b =  0.258   95% CI  0.091 to  0.424   p = 0.003
  poverty_rate        b =  0.043   95% CI -0.044 to  0.130   p = 0.324
  pct_lila_tracts     b =  0.004   95% CI -0.020 to  0.029   p = 0.720

  All four predictors were significant unadjusted. Only educational
  attainment and uninsured rate remained significant when adjusted for
  one another.

Assumption checks
  Breusch-Pagan p = 0.065   homoscedasticity met
  Shapiro-Wilk  p = 0.595   normality of residuals met
  VIF           1.41 to 2.00   no multicollinearity

Logistic regression, binary outcome split at the state median (12.0%)
  29 high counties, 29 low counties
  McFadden pseudo-R2 = 0.491, AIC = 50.96
  Hosmer-Lemeshow p = 0.244, model fits adequately
  Classification accuracy 82.8%

  pct_bachelor_plus   OR = 0.83   95% CI 0.72 to 0.92   p = 0.003
  uninsured_rate      OR = 1.69   95% CI 1.07 to 3.14   p = 0.053

--- Limitations ---
Ecological design. All associations are between county-level rates and
  cannot be interpreted as individual-level risk.

Sample size. n = 58 counties. The median split produces 29 events, so
  events per predictor variable is 7.2, below the conventional threshold
  of 10. The linear model is treated as the primary analysis and the
  logistic model as supporting.

Cross-sectional with mixed vintages. Source years differ (ACS 2020-2024,
  PLACES 2021-2022 BRFSS, USDA Atlas 2019), so temporal ordering between
  predictors and outcome cannot be established.

Influential observations. Cook's Distance flagged 7 counties (Imperial,
  Modoc, Mono, Santa Barbara, Sierra, Trinity, Yolo). In the sensitivity
  analysis excluding them, uninsured_rate weakened from 0.258 to 0.117
  and lost significance, while pct_bachelor_plus held (-0.047 to -0.053,
  p < 0.001). The education finding is robust; the insurance finding is
  sensitive to a small number of counties.

Population scale. County populations range from roughly 1,200 (Alpine)
  to 10 million (Los Angeles). Unweighted county-level models give equal
  weight to each county regardless of population.

--- Known data notes ---
voter_turnout uses CHR&R v177. The toolkit's published code uses v153,
  which is homeownership, not voter turnout.
pct_no_vehicle_far is reported as given by USDA (already a percentage).
  The toolkit's published code multiplies it by 100 in error.
mean_dist_supermarket is a population share, not a distance, despite
  the variable name. Retained under the original name for compatibility.
frl_total is the free and reduced lunch table total, not enrollment.
pct_free_lunch and pct_reduced_lunch use frl_total as denominator, so
  they sum to 100 by construction and are not comparable to an
  enrollment-based rate.

Seven corrections to the published toolkit code are documented in full
in the change log accompanying this repository.

--- GitHub repository ---
Repository URL: https://github.com/kunsanji/CA_Dashboard_Demo
Visibility:     Public

--- Tableau Public dashboard ---
URL:            public.tableau.com/views/__________
Downloads:      enabled

--- Zenodo DOI ---
DOI:            https://doi.org/10.5281/zenodo.22985513

--- License ---
Code: MIT License, see LICENSE.
Data: derived from public U.S. federal and academic sources listed above.
Each retains its own terms. Cite the original source, not this repository,
when reusing the underlying data.

--- Reproducing this analysis ---
1. Register a free Census API key at
   https://api.census.gov/data/key_signup.html
2. Download the raw files listed above into data/raw/
3. Create the folders data/clean, data/master, output/tables,
   output/figures, output/tableau and dictionary
4. Run the scripts in the order listed above
Every script prints its row count. All should report 58.

--- R environment ---
R version: 4.5.3
OS:        Darwin 27.0.0 (macOS)

--- Package versions ---
tidycensus        1.8.1
tidyverse         2.0.0
haven             2.5.5
readxl            1.5.0
janitor           2.2.1
skimr             2.2.2
corrplot          0.95
car               3.1.5
broom             1.0.13
lmtest            0.9.40
ResourceSelection 0.3.6
sandwich          3.1.3
