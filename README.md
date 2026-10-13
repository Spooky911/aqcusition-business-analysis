# Acquisition Business Analysis

An acquisition assessment needs to explain the business behind the transaction totals: who keeps using the service, what drives payment activity, where operational exceptions occur, and how concentrated the activity is.

This project examines the historical operating data of a fintech company being considered for acquisition. The work was divided into two periods:

| Workstream | Period | Coverage in this project |
|---|---|---|
| Historical assessment | January 2010-October 2019 | Completed |
| Subsequent assessment | November 2019-June 2026, the end of Q2 2026 | Assigned to a separate workstream; not analyzed here |

The deliverable is a native Power BI report with **19 pages, 94 DAX measures, 16 tables and 7 relationships**. It retains all **13,305,915 transaction records** in the historical period.

A separate **56-page print report** expands long tables across pages, covering every scoped month, all 60 first-observed cohorts and all 109 merchant categories. It includes page bookmarks, in-page analysis and recommendations on the final page. [Read the print report](report/Fintech_Acquisition_Print_Report.pdf).

## The data challenge

The transaction source alone was approximately **1.26 GB**. Together with the fraud-label file, customer data, card data and merchant-category definitions, the supplied files totaled approximately **1.42 GB**. Loading a small sample would have made the report easier to build, but would also have changed customer continuity, concentration and rare-event results.

The analysis therefore used the complete transaction population. Python processed the files in chunks, checked relationships and calculated detailed audit and comparison tables. Power BI retained the transaction-grain model, while smaller supporting tables made cohort, growth-contribution and risk analysis easier to inspect. Splitting the public data files by year is a distribution choice; it does not reduce the report's coverage.

The source contained 2,000 customers and 6,146 cards. Only 1,219 customers had transactions during the assessment period. That difference matters when interpreting retention: a high repeat-activity rate among transacting customers is not a company-wide retention rate.

## What was checked before interpreting the results

The audit covered transaction identifiers, required fields, date and amount parsing, customer and card links, card ownership, merchant-category mapping, channel values, geographic-field completeness, calculated ages and the alignment of transaction dates with recorded card-opening dates. Customer and card identifiers and date fields were also checked separately.

The structural checks found no duplicate transaction IDs, invalid transaction dates, unparseable amounts, missing customer/card links, card-owner mismatches or unmapped merchant-category codes. Other checks identified questions that the source history cannot resolve:

- **309 transactions** predate the recorded card-opening month.
- **788 transactions** have a calculated customer age above 100; one has a calculated age below 18. Birth-day precision is unavailable.
- **1,652,706 transactions** have no recorded merchant ZIP code.
- **781 source customers** have no transactions in the scoped file.

These are separate checks, not additive exception totals. A clean join does not authenticate the file as production history, and a date mismatch does not by itself establish fraud or an invalid transaction.

## How growth was explained

Annual review separates transaction value, record count, active customers, positive transactions per customer and mean positive transaction value. Full years are compared with full years; January-October 2019 is compared with the same months of 2018.

The value decomposition uses this identity:

**Positive value = active customers x positive transactions per active customer x mean positive transaction value.**

The contribution of each factor is averaged across all six possible orders of changing the factors. The resulting contributions reconcile to the actual change in value rather than assigning the interaction effect to whichever factor is changed first.

In **2017**, record count increased **0.52%**, while positive value decreased **0.22%**. Mean positive transaction value fell from **$50.53 to $50.17**. The ticket-size contribution of about **-$0.48M** outweighed the frequency contribution of about **+$0.38M**. More transactions did not translate into more payment value.

Across 2010-2018, positive value grew at a **1.13% compound annual rate**. January-October 2019 positive value was **$55.32M**, down **0.26%** from the matching 2018 period; record count was down **0.16%**.

## Customer activity and the limits of retention

January 2010 began with **1,083 active customers**. A further **136 customers** first appeared later in the supplied history. Monthly transitions recorded **52 reactivation events** and **68 transitions to inactivity**; these are customer-month events, so the same customer may appear more than once.

Weighted next-month activity was **99.95%**. The median was 100% because **74 of 117** month-to-month comparisons had no customer drop-off. Keeping the median as the sole headline would have hidden the small number of inactivity events, so the report displays the weighted rate and explains both measures.

Fixed-denominator cohorts were followed at months 1, 3, 6, 12 and 24. Overall month-12 activity was **99.67%** among eligible cohorts. Later cohorts were small: month-12 activity was 8 of 9 customers for the 2015 first-observed group and 5 of 6 for 2016. One customer's activity therefore changes those rates materially.

First observation is not proof of acquisition. The January 2010 group includes customers already active when observation began, and a month without transactions is not proof of account closure or contractual churn.

## Operational and fraud exposure

The file contained **211,393 records with error text**. The most frequent error type was **Insufficient Balance**, appearing in **131,621 records**. **Technical Glitch** appeared in **26,633 records**. Records with multiple error types contribute to each relevant type; token counts must not be added to calculate distinct error records.

Online transactions had an error rate of **2.28%**, compared with **1.51%** for chip and **1.49%** for swipe. Technical Glitch rates were close to **0.20%** in all three channels. The higher overall online error rate therefore did not correspond to a higher rate of explicitly recorded technical errors.

Fraud labels covered approximately **67%** of records. Unknown labels remained unknown and were excluded from the fraud-rate denominator.

Online activity accounted for **11.7% of all records** but **65.8% of Yes fraud labels**. Within labeled records, the fraud rate was **0.84%** for online, **0.10%** for chip and **0.03%** for swipe. Online positive transactions above $1,000 had 88 Yes labels among 1,263 labeled records, a **6.97%** rate. The report shows sample counts and label coverage alongside segment rates.

Error-associated positive value was **$13.41M**; fraud-labeled positive value was **$1.61M**. These are values associated with flagged records, not measured company losses. Final settlement, retries, chargebacks, recovery and liability allocation were not supplied.

## Concentration, seasonality and changes over time

Merchant categories and merchant IDs answer different questions. In January-October 2019, the largest ten merchant IDs accounted for **29.08%** of positive value; the largest hundred accounted for **61.36%**. This exposes dependency that a broad category distribution can conceal. A transaction destination is not necessarily a contracted merchant relationship.

Monthly seasonality was assessed using value per calendar day, normalized within each complete year from 2010 through 2018. June had the highest average index at **101.5**, and January the lowest at **98.3**, against an annual mean of 100. Annual normalization does not remove within-year growth, so this is a descriptive pattern rather than an isolated seasonal effect. Weekday workload was also divided by the number of calendar occurrences.

The largest same-month value decline was **July 2014, at -2.51%**; record count declined **0.49%**. Error and technical-error rates were compared with the change without asserting an unverified technical cause. The supplied records do not establish gateway downtime or explain every change in activity.

## Payment movement is not company profit

| Recorded amount | Historical total |
|---|---:|
| Positive transaction entries | $639.35M |
| Negative entries, absolute value | $67.52M |
| Net signed transaction entries | $571.84M |

The net amount is an arithmetic reconciliation of signed transaction values. It is not company revenue, profit, a bank balance or operating cash flow. The files do not establish whether negative entries are refunds, reversals or other adjustments.

Earned fees, commissions, recognized revenue, processor charges, operating expenses and settlement balances were absent. Customer income, debt and credit limits are customer attributes, not company financial statements. The report therefore leaves company profitability uncalculated and includes a financial bridge explaining the missing evidence.

## Report structure

The report moves from overview and audit into annual history, growth drivers, customer continuity and lifecycle, cohort activity, merchant exposure, channel errors, fraud segmentation, seasonality, monthly changes and recorded money movement. A separate profitability page explains the financial-data gap. The final page contains the assessment recommendations.

Numerical business insights sit beside the relevant visuals. They distinguish observed conditions from causal explanations and transaction activity from financial performance. Recommendation text is reserved for the final report page.

## Public files and reproducibility

The public package includes the PBIX deliverable, editable Power BI project definitions, the 94 measure definitions, sanitized transaction data split into compressed yearly files, supporting dimensions, audit results, analytical tables and processing scripts.

Card numbers, CVV, street addresses and exact customer coordinates are excluded. The public transaction tables preserve transaction grain and linkable analytical identifiers; the report uses the full historical population.

To rebuild, run `python scripts/restore_data.py`, then `python scripts/audit.py --data-dir data --output-dir analysis`, followed by `python scripts/build_analysis.py --analysis-dir analysis --data-dir data`. Open the `.pbip` project and configure its `DataRoot` parameter before refreshing. The PBIX already contains the loaded historical model.

The PBIX is a large binary and is intended for a release asset rather than an ordinary Git file. The source project excludes local caches, credentials and machine-specific query paths. The subsequent November 2019-Q2 2026 workstream must use the same metric definitions and a non-overlapping date boundary.
