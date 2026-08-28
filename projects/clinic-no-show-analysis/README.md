# The Empty Chair

**Quantifying and Reducing the Cost of Patient No-Shows Across a UK Private Clinic Network**

An end-to-end analysis of appointment no-shows across a five-site UK private clinic
network: what they cost, what actually drives them, and which interventions pay for
themselves.

**[Read the full report →](https://weston-nyabeze.github.io/projects/clinic-no-show-analysis/clinic_no_show_analysis.html)**

## The question

A missed appointment costs a private clinic twice: the unbilled consultation fee and the
clinician and room left idle. The useful question isn't "what is our no-show rate", it's
"which of the levers we actually control moves it, and is the intervention cheaper than the
loss it prevents".

## Headline findings

| Measure | Value | Basis |
|---|---|---|
| Appointments analysed | 10,000 | 2,890 patients across 5 sites |
| No-show rate | 21.4% | 1 in every 4.7 appointments |
| Gross economic exposure | £298,813 | £221,343 fees plus £77,470 idle staff |
| Slots recovered by rebooking | 41.5% | 890 of 2,143 missed slots refilled |
| **Net economic loss** | **£206,896** | Gross exposure net of rebooking |
| **Net loss per appointment** | **£20.69** | The figure that belongs in the operating budget |

The per-appointment figure is the one that scales: it holds whether the network books ten
thousand appointments a year or twenty thousand, which the lump sum does not.

## What drives it

Three operational factors carry nearly all the explanatory power, and all three are set by
the clinic rather than the patient:

- **Booking lead time.** 26.0% of appointments booked 22 or more days out are missed,
  against 16.4% booked within three days, rising stepwise in between.
- **Reminder timing.** A 72-hour reminder is associated with a 2.6pp lower no-show rate
  than 24 or 48 hours (p = 0.0063). This is the cleanest result in the report, because
  timing is assigned by the clinic in roughly equal numbers regardless of patient type.
- **Reminder response.** Patients who confirm miss 16.6%; patients who cancel but are left
  on the book miss 29.7%. Large, but partly self-selection, so it is used here for *ranking
  risk* and never quoted as a promised saving.

Two results run against instinct and are worth stating plainly. Patients with a chronic
condition on file are **more** reliable, not less (16.2% vs 23.6%, OR 0.62), because an
ongoing treatment relationship gives them a reason to attend. And several intuitive levers
do nothing measurable: a second reminder moves the rate 0.5pp, statistically
indistinguishable from zero, and clinic site, day of week and patient age don't move risk
usably at all. A null result says where *not* to spend, which is worth as much as a
positive one.

Of 19 candidate factors, only 10 do measurable work.

## The model

Four families were fit and compared. Every patient's appointments were kept entirely within
one of three groups (training, calibration, holdout), so no patient straddles the split and
repeat attenders can't leak across it.

| Model | Holdout AUC | 95% CI | Verdict |
|---|---|---|---|
| **Logistic regression** | **0.634** | 0.608 to 0.660 | **Deployed** |
| Elastic net | 0.634 | 0.608 to 0.660 | Statistically tied |
| Random forest | 0.629 | 0.603 to 0.655 | Statistically tied |
| Gradient boosting (XGBoost) | 0.627 | 0.601 to 0.653 | Statistically tied |

Scored on 2,538 held-out appointments from patients none of the models had seen. Selection
rule: take the best scorer, keep everything not measurably worse, deploy the simplest of
those, so the choice doesn't hinge on which candidate got lucky on one sample.

0.634 is honest but modest, and the fact that a tuned gradient-boosting ensemble *couldn't
beat it* is a finding rather than a disappointment: it says the relationship between these
operational factors and attendance is simple and additive, so nothing is paid in accuracy
for using the version a clinician can read and check by hand. Probabilities were calibrated
on held-back data before being used for anything financial, so a predicted 25% risk behaves
like roughly one in four.

## The business case

A genuine no-show costs about £97 in lost value; a wasted call to a patient who would have
attended costs about £4.00. At roughly 24:1, the right policy isn't to call everyone or the
top 5%. It's to solve for the risk score where a call covers its cost.

At the optimal 20.0% threshold the clinic contacts 52.3% of its book and catches 67.9% of
appointments that go on to be missed. Only 28.5% of those calls reach a genuine no-show,
which sounds poor but isn't a flaw given the 24:1 asymmetry.

| Play | Annual value | Annual cost | Net benefit |
|---|---|---|---|
| 1. Default reminder to 72 hours | £13,501 | £0 | £13,501 |
| 2. Extend reminder coverage to everyone | £12,502 | £118 | £12,384 |
| 3. Targeted outreach on the flagged tail *(pilot first)* | £28,377 | £20,930 | £7,447 |
| 4. Auto-release slots on cancellation | £12,986 | £2,500 | £10,486 |
| **Combined** | | | **£43,818** |

That's 21.2% of the current net loss. The split matters more than the total: **£36,371
(17.6%) needs no pilot and no new evidence**, just three operational settings and a
decision, while the remaining £7,447 is conditional on a trial.

Play 3 is gated deliberately. Its value rests on an assumed 20% behavioural response to a
call, which nothing in this data can confirm, and a sensitivity sweep across call cost and
behavioural effect returns "do not run" in 20 of 48 combinations. It should go out as a
randomised trial (call half the flagged appointments, hold the other half back, measure the
difference) before it scales.

## Files

| File | Description |
|---|---|
| `clinic_no_show_analysis.qmd` | Quarto source: all analysis, modelling, and prose |
| `clinic_no_show_analysis.html` | Rendered report (self-contained) |
| `uk_private_clinic_no_show_2026.csv` | Appointment-level dataset, 30 columns |

## Data

Appointment-level records across five clinic sites, 30 columns covering scheduling,
reminders, patient history, and outcome.

**This dataset is simulated, not real patient data.** It has no missing values and exact
arithmetic relationships between fields. The method is built to run on a real extract, but
the specific pounds and percentages here are illustrative rather than any network's
confirmed budget line. There are no names, full postcodes, dates of birth, or free-text
notes; patients appear as surrogate integer IDs and location only as outward postcode
district.

Three data issues were found in audit and handled rather than papered over: two
patient-history columns arrived empty and were dropped, so risk is modelled per
*appointment* rather than per "unreliable patient"; three financial columns are derived from
the outcome itself and would be circular as predictors, so they size the loss but never feed
the model; and overlapping reminder fields were consolidated into one clean signal.

## Reproducing

Requires R with `data.table`, `ggplot2`, `scales`, `glmnet`, `ranger`, `pROC`, `knitr`,
`kableExtra`, `patchwork`, and `plotly`.

```bash
quarto render clinic_no_show_analysis.qmd
```

Seed `1234568` governs every stochastic step, so the reported figures reproduce exactly.
Every number in the report is computed by the code that renders it, not typed by hand.

## Limitations

The data is simulated, so the figures are illustrative. The confirmation gap mixes a real
effect with self-selection and is used for ranking only. No patient-history segment could be
built from this extract. And the business case rests on one quantity the data cannot supply,
namely how much a call actually changes behaviour, which is precisely what the recommended
pilot is designed to measure.

---

Part of my [analytics portfolio](https://weston-nyabeze.github.io).
