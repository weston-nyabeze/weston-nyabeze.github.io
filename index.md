---
layout: default
title: Weston T. Nyabeze | Business Analytics & AI
---

# My Business Analytics Portfolio

### Learn About My Projects

#### [The Empty Chair: Clinic No-Show Analysis](https://github.com/weston-nyabeze/weston-nyabeze.github.io/tree/main/projects/clinic-no-show-analysis)

I analysed 10,000 appointments across a five-site UK private clinic network in **R** and **Quarto** to price and predict patient no-shows. A 21.4% no-show rate carried £206,896 of annual loss after rebooking recovery, or £20.69 on every appointment booked. I compared four model families on a patient-level split, deployed logistic regression at 0.634 holdout AUC because a tuned XGBoost couldn't measurably beat it, and turned the risk scores into four costed plays worth £43,818 a year, £36,371 of which needs no pilot. [Read the report](/projects/clinic-no-show-analysis/clinic_no_show_analysis.html).

#### [Modelling Chronic Disease Burden from Area Level Determinants](https://github.com/weston-nyabeze/weston-nyabeze.github.io/tree/main/projects/chronic-disease-modelling)

I modelled chronic disease burden across the 1,473 census tracts of the Washington metro in **Python**, then tested whether the fit meant anything. A six-condition burden index built from the CDC PLACES 2025 release held together at 0.966 alpha, and six estimators compared under both random and county-blocked validation put elastic net ahead at 0.947 while gradient boosting fell ten points behind. Three circularity diagnostics showed that most of the fit comes from shared parentage rather than aetiology: a placebo set of seventeen unrelated measures predicted the index at 0.932 across held out counties, against 0.926 for the nineteen designed determinants. [Read the report](/projects/chronic-disease-modelling/DMV_Chronic_Disease_Modelling_report.html).

#### [The Gap That Didn't Close: Income, Race and US Adult Obesity](https://github.com/weston-nyabeze/weston-nyabeze.github.io/tree/main/projects/obesity-disparity-analysis)

I tracked fourteen years of CDC BRFSS data in **R** and **Quarto** to ask which social gaps in adult obesity are actually closing. National prevalence rose from 27.4% to 33.1%, and two independent forecasting methods put 2030 near 36%. The income gap looked like it collapsed, from 9.5 points to 2.7, but a composition check traced that to BRFSS replacing its income question in 2021: the top bracket's share of respondents fell from 31.6% to 11.6% in a single year while unreported income rose from 16.1% to 38.5%. Within one instrument regime the gap is flat. Race, not income, is the strongest social predictor across a linear model, a random forest and gradient boosting, and neither the race gap nor the education gap is projected to close by 2030. [Read the report](/projects/obesity-disparity-analysis/obesity-disparity-analysis.html).

### My Projects

[The Empty Chair: Clinic No-Show Analysis](/projects/clinic-no-show-analysis/clinic_no_show_analysis.html)

[Modelling Chronic Disease Burden from Area Level Determinants](/projects/chronic-disease-modelling/DMV_Chronic_Disease_Modelling_report.html)

[The Gap That Didn't Close: Income, Race and US Adult Obesity](/projects/obesity-disparity-analysis/obesity-disparity-analysis.html)

### Resume

[Read my resume](/resume.html) · [Download the PDF](/files/Weston_Nyabeze_Resume.pdf)

### LinkedIn

[View My LinkedIn Profile](https://www.linkedin.com/in/weston-t-nyabeze)

### GitHub

[View My GitHub Profile](https://github.com/weston-nyabeze)
