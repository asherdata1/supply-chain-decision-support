# Olist customer-experience risk

This project examines how Olist could prioritize limited customer-experience
monitoring and seller-support resources using historical marketplace data.

- **Order monitoring (primary):** Rank incoming orders by the probability of an
  eventual observed 1–2-star review using information reasonably available at
  placement.
- **Seller support (secondary):** Rank historically active sellers by the
  probability of at least one observed low-review event among the next month's
  orders, allowing an additional month for review arrival.

The seller target measures observed event incidence, not underlying seller
quality. Neither analysis establishes seller fault or intervention effectiveness.

## Deliverables and repository layout

| Location | Purpose |
|---|---|
| `03_final/Olist_Technical_Notebook.ipynb` | Primary technical handoff: preprocessing, EDA, modeling, technical briefs, and conclusions. |
| `02_notebooks/01_preprocessing.ipynb` | Supporting source inspection and baseline construction. |
| `02_notebooks/02_eda.ipynb` | Supporting exploratory analysis. |
| `02_notebooks/03_modeling.ipynb` | Supporting order- and seller-level modeling. |
| `01_data/raw/` | Nine original Olist CSV files, including category translations. |
| `01_data/preprocessed/` | Shared order-item baseline produced by preprocessing. |

The consolidated notebook is the submission source of truth. Its final editorial
updates may differ from the supporting notebooks. The technical PDF, business
brief, and presentation are separate submission artifacts. The AI reflection log
is maintained separately; the notebook includes an AI assistance disclosure.

## Setup

The notebook was validated with **Python 3.11.15**. From the repository root,
create an environment and install the recorded dependencies:

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python -m ipykernel install --user --name olist-team09 --display-name "Olist Team 09"
```

On Windows, activate the environment using `.venv\Scripts\activate` instead.
Dependency versions identify the validated environment; other versions may change
results or require compatibility adjustments.

## Data and execution

The raw CSVs are included under `01_data/raw/`. If obtaining a fresh dataset,
download the Olist dataset linked below and place its nine CSV files directly in
that directory, preserving their original names. No credentials or API are
required by the notebook itself.

From the repository root:

```bash
cd 03_final
python -m jupyterlab Olist_Technical_Notebook.ipynb
```

Select the **Olist Team 09** kernel and run all cells in order. Keep `03_final/`
as the notebook's working directory. Preprocessing writes
`../01_data/preprocessed/olist_order_item_baseline.csv`; subsequent sections read
that baseline and the raw tables. Results, charts, and tables remain inline;
no separate outputs folder is required. Grid searches and permutation importance
are the computationally intensive steps; runtime depends on the machine.

The consolidated notebook has completed an end-to-end validation run. It contains
all imports in its first cell and a linked table of contents. To use the modular
workflow instead, run the three supporting notebooks in numerical order from
`02_notebooks/`. The final consolidated notebook remains authoritative for
submission interpretations.

## Main findings and decision limits

At top-10% order-monitoring capacity, the selected gradient boosting model captures
24.25% of low reviews versus 20.75% for item-count ranking on the random holdout.
On the chronological holdout, it captures 20.54% versus 21.64% for the simple rule.
Useful ranking signal therefore does not establish a consistent incremental
advantage over item count. The later-period evaluation is a retrospective
sensitivity analysis using settings selected on the random training sample.

Basket complexity remains associated with low reviews among timely deliveries.
These descriptive findings motivate operational investigation, not causal claims.
Calendar-date delivery comparisons exclude orders without the dates needed for
classification; missing reviews are unknown outcomes, not favorable reviews.

The seller analysis uses its own historical features, eligibility, target, and
chronological splits. Its performance is not directly comparable to the order
model. Activity creates opportunities for future low-review events, so an
activity-ranking baseline and prospective intervention evaluation are prerequisites
for claims of operational benefit. No integrated order/seller scoring system or
intervention effect is evaluated.

## Team contributions

- **Asher Frank:** Order-level preprocessing, EDA, placement-time modeling,
  simple-rule benchmarks, and follow-up basket-complexity analyses; consolidation
  of the technical narrative and business deliverables.
- **Bereket Tarekegn:** Seller-level exploration, historical feature and target
  construction, seller modeling and temporal evaluation; review corrections and
  initial technical report drafting.

Both contributors reviewed and refined the project. Git history preserves its
development; the notebooks describe the final analytical decisions.

## Data source

Olist. (2018). *Brazilian E-Commerce Public Dataset by Olist* [Data set].
[Kaggle dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce).

[Team repository](https://github.com/asherdata1/supply-chain-decision-support).
