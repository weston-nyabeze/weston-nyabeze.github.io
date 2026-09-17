---
layout: default
title: Healthcare Operations & Performance Dashboard | Weston T. Nyabeze
---

# Healthcare Operations & Performance Dashboard

**Tools:** Excel, Power Query, Power Pivot, DAX, PivotTables, PivotCharts, slicers

[Download the workbook](healthcare_operations_dashboard.xlsx) · [Back to portfolio](/)

## Overview

I built an interactive Excel dashboard from 55,500 patient admission records covering May 2019 to May 2024. It tracks admissions, billing, hospitals, doctors, length of stay and insurance provider performance on a single screen, with Year and Month slicers so a reader can move from the five year picture to a single month in two clicks. The goal was to take a flat extract and turn it into something an operations lead could open on a Monday morning and read without touching a formula.

## Data and preparation

The source is a publicly available synthetic healthcare dataset with one row per admission: medical condition, admission and discharge dates, doctor, hospital, insurer, billing amount, room, admission type, medication and test result.

I loaded and cleaned it in Power Query, then split the flat file into a star schema. `Patients_Data` is the fact table, keyed to separate patient, doctor and hospital dimension tables, an insurance provider lookup, and a `Date_Dim` calendar table carrying year, quarter, month, weekday and weekday or weekend flags. Length of stay is derived from the admission and discharge dates, and patients are banded into age groups. All of the tables sit in the Power Pivot data model with relationships defined there, so every PivotTable on the dashboard reads from the same model and responds to the same slicers.

## DAX measures

The KPIs come from a dedicated measures table rather than from worksheet formulas:

- Admissions, total billing, average length of stay, distinct doctors and distinct hospitals
- Emergency admission rate, long stay rate and abnormal test result rate
- Average admissions per day, average discharges per day and net admission rate per day
- Prior year versions of each measure and the matching year over year change

## Dashboard

The dashboard is built on one sheet, with the supporting PivotTables kept on a separate `Analysis` sheet so the front end stays clean.

- KPI cards for admissions, billing, hospitals, doctors, average length of stay and daily admissions, with year over year change
- Admissions and billing by year, with years above the period average highlighted
- Monthly admissions trend with the three busiest months called out automatically
- Admissions by day of week, with the top three days highlighted
- Weekday versus weekend share of billing
- Quarterly billing and each quarter's share of the total
- Total billing by insurance provider
- Year and Month slicers connected across every visual, plus a clear filters control

Call-outs such as the top three months and the above average years are driven by `LARGE`, `INDEX`/`MATCH` and `GETPIVOTDATA` formulas over the PivotTables, so the text updates when the slicers change.

## What the data shows

Across the full period the records carry $1.417 billion in billing, an average of $25,539 per admission and an average stay of 15.5 days. The four complete years each hold about 11,000 admissions; 2019 and 2024 are partial years, which is why they look lower on the yearly chart.

The most useful finding is how little separates the groups an operations team would normally expect to differ. Elective, urgent and emergency admissions each make up about a third of volume and stay within 0.2 days of each other on length of stay. The five insurers each account for between 19.7% and 20.3% of billing, from Aetna at $278.9 million to Cigna at $287.1 million. Weekdays hold 71.5% of admissions and 71.6% of billing, almost exactly the 71.4% that five days out of seven would give on their own. Quarterly billing only moves from 24.5% of the total in the first quarter to 25.4% in the third, and the monthly gap is mostly February being a shorter month.

That evenness is a property of the synthetic source rather than a real hospital network, and the hospital and doctor fields reinforce it: 39,876 distinct hospital names across 55,500 records means nearly every admission has its own hospital, so those counts describe the dataset, not a network. I kept both KPIs on the dashboard because the model and measures are built to handle a real extract with repeating hospitals and doctors, where those cards would become some of the most useful on the page. The dataset also contains 108 records with negative billing amounts, which I left in the totals and would treat as refunds or adjustments to confirm with a billing team on real data.

## Files

- `healthcare_operations_dashboard.xlsx`: the full workbook, including the Power Query steps, data model, DAX measures, analysis sheet and dashboard

The workbook is best opened in desktop Excel for Windows, which fully supports Power Pivot. Excel for Mac and Excel for the web will open it and show the dashboard, but they cannot edit the data model or the DAX measures.
