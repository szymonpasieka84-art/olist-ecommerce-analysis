# Olist E-Commerce Analysis

**[View the rendered notebook on GitHub](https://github.com/szymon-pasieka/olist-ecommerce-analysis/blob/main/01_exploration.ipynb)** — full analysis with charts, code, and findings.

Exploratory data analysis of the [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) — a real public dataset covering ~100,000 orders placed on Olist between 2016 and 2018. The analysis answers two business questions a junior analyst at Olist might be asked to investigate.

## Key findings

- **Revenue is hyper-concentrated geographically.** São Paulo (SP) alone generates BRL 5.77M from delivered orders — more than the next three states combined. The top three states São Paulo (SP), Rio de Janeiro (RJ), and Minas Gerais (MG) account for roughly 77% of revenue across the top 10.
- **Concentration is driven by order volume, not basket size.** São Paulo has the *lowest* average order value (BRL 142) of the top 10 states. Smaller states like Bahia (BRL 182) and Goiás (BRL 171) carry higher AOVs but a tiny fraction of São Paulo's volume.
- **Category revenue is broad rather than specialist.** Across São Paulo, Rio de Janeiro, and Minas Gerais, the top 10 categories span only a 2.5× gap (BRL 880K → BRL 354K), with bed_bath_table, health_beauty, and watches_gifts leading. Olist behaves as a general lifestyle marketplace.
- **Delivery time is a strong predictor of customer satisfaction.** Pearson r = -0.334 across 95,824 orders. Average reviews fall from 4.41 (0–7 days) to 2.17 (31–60 days).
- **Satisfaction has a sharp cliff at 21–30 days, not a gradual decline.** Reviews stay above 4.0 up to 21 days. Past 30 days, 56% of customers leave a 1-star review. The operational implication: preventing slippage past the 21-day threshold matters far more than shaving days off already-fast deliveries.

## Questions explored

1. **Where is revenue concentrated, and what does the top tier look like?**
   Top states by revenue, average order value comparison, and the product-category breakdown within the top three states.
2. **Does faster delivery actually correlate with better reviews?**
   Computing delivery time from order timestamps, distribution analysis, bucketed review-score comparison, and correlation testing (Pearson + Spearman).

## Data

The dataset is relational — five tables joined on order, customer, product, and review keys:

- `olist_orders_dataset` — one row per order, with purchase and delivery timestamps and status
- `olist_order_items_dataset` — one row per item, with price and freight
- `olist_customers_dataset` — customer location, including state
- `olist_products_dataset` — product attributes, including category
- `olist_order_reviews_dataset` — review scores (1–5) per order

A category-name translation file maps Portuguese category names to English.

## Methods

- Loading and joining five relational tables on order/customer/product/review keys
- Datetime parsing and per-order delivery time computation
- Data cleaning: impossible-value filtering, duplicate review deduplication
- Aggregations with `groupby` + `.agg`, ranking, and bucket analysis with `pd.cut`
- Visualization: bar charts (vertical + horizontal), histograms with mean/median markers
- Correlation analysis: Pearson (linear) and Spearman (rank-based) with sample size reporting

## Repo structure

```
.
├── 01_exploration.ipynb   # The analysis — runs top to bottom
├── README.md              # You are here
├── requirements.txt       # Pinned package versions
├── .gitignore
└── data/                  # Not committed — download from Kaggle (see below)
```

## How to run

1. Download the Olist CSVs from [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) and place them in a `data/` folder at the project root.
2. Set up the environment:

```bash
python -m venv .venv
.venv\Scripts\activate          # Windows
source .venv/bin/activate       # macOS / Linux
pip install -r requirements.txt
```

3. Open `01_exploration.ipynb` in Jupyter or VS Code and run all cells.

## Tools

- Python 3.12
- pandas, numpy
- matplotlib, seaborn
- scipy (for correlation tests)
- Jupyter / VS Code

## Caveats

- The analysis is filtered to delivered orders for revenue work; cancelled and incomplete orders are excluded.
- The delivery–review finding is correlational, not causal — slow deliveries likely co-occur with other quality issues that independently affect reviews.
- 1,559 items in the category analysis lacked an English translation and were excluded.
- The 60+ day delivery bucket has a small sample (n=273) and should be read cautiously.

## Possible extensions

- **Causal analysis of delivery on reviews** — isolate the delivery effect from co-occurring quality issues using review-text analysis or a quasi-experimental design.
- **Seller-level segmentation** — identify whether a small number of sellers drive the long delivery tail and the resulting poor reviews.
- **City- or zip-level granularity** — test whether São Paulo's dominance is metro-driven or spread across the state.
- **Time-series view** — track whether delivery performance and revenue concentration shifted across the 2016–2018 window.