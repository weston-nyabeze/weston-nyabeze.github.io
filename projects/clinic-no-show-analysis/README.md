# The Empty Chair — Predicting and Pricing Patient No-Shows

An end-to-end analysis of appointment no-shows across five UK private clinics: what they
cost, what actually drives them, and which interventions pay for themselves.

**[Read the full report →](https://weston-nyabeze.github.io/projects/clinic-no-show-analysis/clinic_no_show_analysis.html)**

## The question

A missed appointment costs a private clinic twice — the unbilled consultation fee and the
idle clinical time. The operational question isn't "what is our no-show rate", it's "which
of the levers we actually control moves it, and is the intervention cheaper than the loss".

## Headline findings

| Measure | Value |
|---|---|
| Appointments analysed | 10,000 across 8,471 patients and 5 sites |
| No-show rate | 17.8% |
| Gross exposure (lost fees + idle staff) | £356,800 |
| Slots recovered by rebooking | 48.6% |
| **Net annual loss** | **£183,440** (£18.34 per appointment booked) |

The drivers that matter are operational, not demographic. Reminder response is the
strongest single signal — patients who confirm no-show at 8.9%, those who cancel at
booking at 28.8%. Booking lead time runs the same direction: 9.1% for appointments booked
0–3 days out versus 25.5% at 22+ days. Moving the reminder from 24/48 hours to 72 hours is
worth 4.9 percentage points on its own.

Several intuitive candidates turned out to be non-drivers — clinic site, day of week,
patient age, and sending a second reminder all landed at or below 0.7pp. A chronic
condition flag was mildly *protective* (14.0% vs 19.1%), which is the kind of result worth
stating explicitly so nobody builds a policy on the opposite assumption.

## Recommendations and their economics

| Play | Annual value | Annual cost | Net |
|---|---|---|---|
| Default reminder lead to 72h | £22,560 | £0 | £22,560 |
| Extend reminders to all patients | £18,120 | £480 | £17,640 |
| Risk-targeted outreach *(pilot first)* | £31,440 | £2,600 | £28,840 |
| Auto-release cancelled slots | £19,200 | £2,500 | £16,700 |
| **Combined** | **£91,320** | **£5,580** | **£85,740** |

That recovers 46.7% of the current loss, with three of the four plays deployable
immediately and the targeted-outreach play gated behind a pilot.

## Method

Four model families were fit and compared: unpenalised logistic regression, elastic net,
random forest, and gradient boosting. Data was split by *patient ID* rather than by row, so
no patient appears in both training and holdout — without that, repeat attenders leak
across the split and inflate the score.

Elastic net was selected on holdout AUC of **0.696 [95% CI 0.672–0.720]**, with Platt
calibration applied on the holdout set and permutation importance used for feature ranking.

An AUC near 0.70 is a genuinely useful signal but not a precise one, and the business case
is built accordingly: it assumes a modest 20% behavioural uplift on contact and reports the
12% breakeven, so the recommendation survives the model being mediocre. At the selected 29%
risk threshold the model flags 26.2% of the book and catches 63.0% of true no-shows.

## Files

| File | Description |
|---|---|
| `clinic_no_show_analysis.qmd` | Quarto source — all analysis, modelling, and prose |
| `clinic_no_show_analysis.html` | Rendered report (self-contained) |
| `uk_private_clinic_no_show_2026.csv` | Appointment-level dataset, 30 columns |

## Data

Appointment-level records covering five clinic sites. The dataset is anonymised: patients
appear only as surrogate integer IDs, location is limited to the outward postcode district
(e.g. `B15`), and there are no names, full postcodes, dates of birth, or free-text notes.
Two columns shipped empty (`patient_previous_noshow_count`, `patient_noshow_rate`) and are
dropped in the analysis rather than imputed.

## Reproducing

Requires R with `data.table`, `ggplot2`, `scales`, `glmnet`, `ranger`, `pROC`, `knitr`,
`kableExtra`, `patchwork`, and `plotly`. Then:

```bash
quarto render clinic_no_show_analysis.qmd
```

Seed `1234568` governs every stochastic step, so the reported figures reproduce exactly.

## Limitations

The data is observational, so every effect here is an association rather than a causal
estimate — the 72-hour reminder result in particular is the one most worth confirming with
a randomised trial before it's treated as a lever. Cost figures rest on stated consultation
fees and a modelled staff idle rate, and the benefit projections assume behavioural uplifts
that only a pilot can measure.

---

Part of my [analytics portfolio](https://weston-nyabeze.github.io).
