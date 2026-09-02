# Modelling Chronic Disease Burden from Area Level Determinants

A study of the 1,473 census tracts of the Washington metropolitan area, and of how much of the
apparent predictive power in model based small area health data is real.

**Weston Nyabeze** · MS Business Analytics and Artificial Intelligence, Kogod School of Business,
American University

---

## The question

An analyst asked to explain neighbourhood chronic disease from behavioural, healthcare access and
social risk factors, using the CDC PLACES release, will obtain a very high fit and, on standard
practice, will report it. This project asks whether that fit means what it appears to mean.

PLACES estimates are outputs of a multilevel regression and poststratification model, not
measurements. When one PLACES measure is regressed on several others, both sides of the equation
descend from the same fitted model and the same demographic inputs, so the association may be
epidemiological or it may be an artefact of shared parentage. The project measures that effect
directly with three diagnostics that need no data beyond the release itself.

## What the analysis found

**1. Three candidate conditions do not belong in a chronic disease index.** Nine diagnosed conditions
were tested for scale membership. Asthma, cancer and high cholesterol fail item analysis: asthma is a
different aetiology, and cancer and high cholesterol are subject to ascertainment bias, running
highest where preventive screening is best. Removing them raises Cronbach's alpha from 0.931 across
all nine to **0.966** for the six retained, and the first principal component explains **85.4 per cent**
of their variance. The index is the unweighted mean of high blood pressure, diabetes, coronary heart
disease, stroke, COPD and arthritis, each z-scored across the metro.

**2. Random cross validation flatters every model, and the ranking only holds under honest
validation.** Six estimators were fitted to the same 19 determinants. Ordinary least squares reports
0.970 under random 10-fold and 0.901 across held out counties; gradient boosting falls from 0.955 to
0.849. Elastic net generalises best across counties at **0.947**, ten points ahead of gradient
boosting, though under random folds the gap is under two points. The common assumption that a
gradient boosting model is the safe default is wrong for this smooth, near collinear problem.

**3. The high fit is largely circular, and is reported as such.** Three tests, none needing outside
data:

- **Effective dimensionality.** Four principal components carry 91 per cent of the variance in all 40
  PLACES measures. The release contains about four facts per tract, presented forty ways.
- **Pairwise redundancy.** The median determinant is reproducible from the other eighteen at an
  R-squared of **0.967**; the five health related social needs measures exceed 0.996. This is what
  produced a maximum variance inflation factor of **1,244** and impossible coefficient signs.
- **Placebo.** Two principal components of seventeen measures with no causal pathway to
  cardiometabolic disease predict the burden index at **0.932** across held out counties, against
  0.926 for all nineteen carefully chosen determinants. Adding the full designed determinant set to
  that two component placebo raises R-squared by only **0.047**.

**4. The models describe the level of burden without explaining its geography.** The outcome is
strongly clustered (Moran's I 0.581, p 0.001). Ordinary least squares removes about half of that
structure in sample; under county blocked validation the residuals of every model are as clustered as
the outcome or more so. Physical inactivity is the one determinant whose association survives every
specification.

## The defensible answer

Behavioural, access and social determinants as measured in PLACES explain a modest increment over the
shared demographic basis of the estimates themselves. Large R-squared values from these variables are
diagnostic of that shared basis rather than of aetiology. The methodological contribution is the
protocol: blocked validation and a placebo predictor set cost minutes to run, need no additional
data, and here were the difference between reporting a strong finding and reporting a true one.

## Files

```
DMV_Chronic_Disease_Modelling.ipynb        the analysis: index construction, model comparison,
                                           circularity diagnostics, spatial residual check
DMV_Chronic_Disease_Modelling_report.html  the finished report, ~13 pages, no code
DMV_Chronic_Disease_Modelling_report.docx  the same report as an editable Word document

figures.py            figure builders (fig1_outcome ... fig5_hierarchical) and the visual system;
                      it computes no result, every function receives finished data and draws it
export_report.py      executes the notebook and renders it to the print ready HTML report
render_all.py          one command: execute, build the HTML report, build the DOCX
report.css            stylesheet for the report export (page column, typography, table wrapping)
requirements.txt      pinned dependencies

data/processed/places_dmv_wide.csv        one row per tract: 40 PLACES measures, lon, lat, population
data/processed/places_dmv_filtered.csv    long format with the published confidence intervals
```

The analysis lives in the notebook. Plotting code sits in `figures.py` so the notebook reads as its
argument rather than as matplotlib boilerplate.

## Running it

```bash
pip install -r requirements.txt
jupyter lab DMV_Chronic_Disease_Modelling.ipynb     # or open it in VS Code
```

Run all cells in order. It takes about two minutes, most of it in the model comparison, the block
decomposition and the permutation importance. Seed 1234568 throughout, so it reproduces exactly.

## Producing the report

```bash
python render_all.py                 # execute, then write the HTML and the DOCX
python render_all.py --no-exec       # skip re-running, just rebuild from current outputs
```

`render_all.py` executes the notebook, renders it to HTML with `--no-input` so the reader sees the
argument rather than the code, drops the reference list (the cell tagged `no-report`), injects
`report.css`, and converts that HTML to a Word file with pandoc. Open the HTML and print to PDF from
the browser (Ctrl+P, Save as PDF, US Letter, margins 13mm, background graphics on), or edit the DOCX.

`export_report.py DMV_Chronic_Disease_Modelling.ipynb` does the HTML step alone. pandoc is not
required for that; the DOCX step uses the copy bundled with Quarto if a standalone pandoc is not on
PATH.

`report.css` also constrains the layout: a single centred page-width column, serif body with sans
headings, and table outputs floated so the surrounding prose wraps beside them while charts stay full
width. Cells are tagged `wl` / `wr` / `pair` to control which side a table floats to.

## Method

- **Data.** CDC PLACES, Local Data for Better Health, Census Tract Data, 2025 release, filtered on
  county FIPS to CBSA 47900: 24 counties, 1,473 tracts, 6,371,256 residents, all measures crude
  prevalence for adults 18 and over. Filtering on FIPS rather than name matters, because Virginia has
  its own Frederick and Montgomery counties that are not in this metro and PLACES files Fairfax County
  and the City of Fairfax under one name.
- **Outcome.** A Chronic Disease Burden Index from six diagnosed conditions, selected by corrected
  item-total correlation and Cronbach's alpha with the item removed (alpha 0.966, first component
  85.4 per cent). Each condition z-scored, the index their unweighted mean.
- **Determinants.** Nineteen measures admitted only if plausibly a determinant rather than a
  consequence, symptom or rename of the outcome, in four blocks: Context (2), Behaviour (5),
  Access (5, service use inverted to gaps), Social (7). Seventeen further measures held back for the
  placebo.
- **Models.** Ordinary least squares (HC3 robust standard errors), ridge, lasso and elastic net with
  internal cross validated penalties, random forest and histogram gradient boosting.
- **Validation.** Random 10-fold and county blocked 5-fold on every model; the gap is reported as
  optimism and is itself a result.
- **Circularity protocol.** PCA of all 40 measures for effective dimensionality; each determinant
  regressed on the other eighteen for pairwise redundancy; a placebo in which leading principal
  components of the seventeen held-back measures predict the outcome under county blocking, computed
  inside a column transformer so components are fitted on training folds only.
- **Diagnostics.** Variance inflation factors; Breusch-Pagan for heteroscedasticity; Moran's I with
  PySAL on an 8 nearest neighbour, row standardised weights matrix, 999 permutations, for the outcome,
  the OLS residuals and the out-of-fold residuals of the blocked models.
- **Interpretation.** A final elastic net with permutation importance over 20 repeats on a held out
  set of counties, reported with the Strobl et al. (2007) caveat that importance is unreliable under
  the collinearity found here.

## Limitations

All inference concerns tracts, never individuals (Robinson, 1950), and nothing here establishes that
changing a determinant would change burden. Tract level PLACES publishes no age standardised values,
so age structure confounds every association; the negative binge drinking and checkup coefficients
are the symptom. The outcome is itself a model output with published uncertainty rather than a
measurement, and those intervals were not propagated. One release year and one metropolitan area: the
dimensionality result is a property of the PLACES methodology and should generalise, but the
coefficients are local. Scores are standardised within this metro and are not comparable to scores
computed elsewhere.

---

Source: Centers for Disease Control and Prevention, PLACES: Local Data for Better Health, Census
Tract Data, 2025 release. Geography per OMB Bulletin 23-01.
