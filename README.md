# Olist customer-experience risk

Predict customer-experience risk early enough to prioritize operational monitoring
and seller support. Asher Frank and Bereket Tarekegn use the same Olist source
files for two complementary decision tasks.

- **Order monitoring (primary):** At order placement, rank reviewed orders by the
  probability of an eventual latest 1–2-star review using placement-time inputs.
- **Seller support (secondary):** At a scoring date, rank historically active
  sellers by the probability of at least one observed low-review event among
  the next month's orders, allowing one additional month for review arrival.

The seller task predicts observed event incidence, not an underlying seller
quality score or low-review rate. Sellers with more orders have more opportunities
for an event. Neither model establishes seller fault or intervention effectiveness.

## Notebook map

Run notebooks with `02_notebooks` as the working directory. Raw CSVs are in
`01_data/raw`; preprocessing writes the shared order-item baseline to
`01_data/preprocessed/olist_order_item_baseline.csv`. Results remain inline.

| Notebook | Role |
|---|---|
| `02_notebooks/asher_preprocessing.ipynb` | Inspect relational structure, consolidate reviews and payments before item joins, document source anomalies, and export the item baseline. |
| `02_notebooks/asher_eda.ipynb` | Shared order-population, review-coverage, calendar, and observed-delivery context. |
| `02_notebooks/asher_modeling.ipynb` | Primary order-risk model comparison, chronological evaluation, interpretation, and timely-delivery sensitivity. |
| `02_notebooks/ADS505_Team09_Bereket_Seller_Model_FINAL.ipynb` | Current seller-history snapshots, chronological model selection/test, and seller prioritization. Reads the same raw tables; seller aggregation requires dated source reviews rather than the baseline's eventual-review summary. |
| `02_notebooks/Bereket_Olist_Complete_EDA_Team09.ipynb` | Earlier seller exploration retained for provenance; not an additional required pipeline stage. |
| `02_notebooks/ADS505_Team09_Olist_End_to_End_Modeling_AsherBereket.ipynb` | Superseded seller approach retained for provenance; do not use its target/results in the final CX narrative. |
| `x_archive` | Earlier DataCo work, preserved separately from the Olist analysis. |

Dependencies are the packages imported by each notebook (Python, pandas, NumPy,
Matplotlib, scikit-learn, and IPython). No `03_outputs` folder is required.

## How the analyses fit together

1. Establish the source-data structure, review population, and delivery context.
2. Show whether placement-time information concentrates low-review risk among
   orders, including orders that ultimately arrive on time.
3. Use seller history for a separate, longer-horizon support queue. Historical
   reviews and completed fulfillment are allowed here only when observable before
   the seller scoring date; they are not placement-time order predictors.
4. Present the two queues as complementary monitoring tools. A direct combination
   of order scores, seller scores, and business value has not been implemented or
   evaluated; do not describe this as a validated integrated scoring system.

Keep the two modeling notebooks separate: their observation grains, eligibility,
review availability, targets, and evaluation dates differ. Reuse shared EDA in
one narrative rather than repeating every raw-data audit in the final report.

## Seller integration review — October 11, 2026

The seller branch was merged locally while preserving both contributors' history.
The current seller notebook's loading path now uses the repository's raw data,
and its outputs were regenerated after the order-level review-attribution fix.
No seller feature definitions, labels, split dates, or model settings were changed.

- **Timing:** At least five orders in the prior 180 days establishes eligibility.
  Scoring dates are July/September/November 2017 and January/March/May 2018.
  Each label uses the scoring month's orders and reviews arriving before two
  months after scoring. The previous cohort's label deadline matches the next
  scoring date; the earlier overlapping-label concern is addressed by this design.
- **Evaluation:** Train 1,702 seller snapshots; validation 1,712; May test 1,030
  (283 positives, 27.5%). Regenerated test AP is about 0.625 and ROC-AUC 0.791.
  The top 103 sellers contain 76 positives: 73.8% precision, 26.9% capture,
  and 2.69× lift. This reproduces the headline result; other capacity table values
  and other metrics should be taken from the regenerated outputs.
- **Value beyond volume:** Ranking by historical order count alone captures
  72 positives among the top 103 (2.54× lift; AP 0.557). The model adds four
  captured positive sellers at this capacity. Report this simple benchmark
  alongside the forest rather than attributing all lift to modeled CX quality.
  Ties in the simple volume ranking make its exact top-capacity count sensitive
  to tie handling.
- **Exposure:** 320 test sellers have no next-month orders; 321 have no reviewed
  next-month orders. All receive zero labels under the documented observed-event
  target. Among sellers with 1–4 future orders, 20.7% have an event; with 20+
  orders, 95.2% do. These descriptive exposure bands are not model predictors.
- **Attribution:** Reviews are selected once per order before attribution to every
  seller on that order. This preserves multi-seller participation, but cannot
  establish which seller caused a poor experience.

### Reconcile before final submission

- The seller notebook compares exact delivery timestamps with estimated-date
  midnight; the order analysis uses calendar dates. Its saved operational EDA
  therefore understates on-time delivery. Calendar-date calculations give 57.2%
  of **all** low-reviewed orders delivered on time, or 67.6% of low-reviewed orders
  **with observed delivery**. State the denominator when using the approximately
  68% claim and use a consistent delivery definition in the final report.
- `hist_unique_products` and `hist_unique_categories` sum within-order distinct
  counts across orders. They are not distinct products/categories across the
  entire historical window; their descriptions should make that clear.
- The monthly label deadline permits approximately 30–61 days of follow-up,
  depending on purchase date. It defines an observed-by-deadline event, not an
  eventual complete-review outcome. Review latency may affect the target.
- Seller-history predictors dominate native importance, while historical review
  rates contribute little. Avoid claiming this identifies deterioration in seller
  quality without further evidence.
- Review-definition sensitivity reuses the May test cohort. After inspection,
  call this a held-out historical test with post-evaluation sensitivity checks,
  rather than an indefinitely untouched test. Seller snapshots also share sellers,
  and multi-seller orders share review outcomes.
- The updated order notebook still contains an association-rule interpretation
  after rule mining was removed. Resolve that leftover narrative and the draft
  report's quantitative claims during final editorial consolidation.

## Data source

Olist. (2018). *Brazilian E-Commerce Public Dataset by Olist* [Data set].
[Kaggle dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce).
