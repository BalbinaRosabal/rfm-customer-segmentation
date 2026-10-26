# RFM Customer Segmentation with Databricks and PySpark

**Databricks · PySpark · Spark SQL / Delta Lake · Customer Analytics | Academic analytics project · 2026**

An end-to-end customer segmentation project using **Recency, Frequency, and Monetary (RFM)** analysis to understand purchasing behaviour in an e-commerce transaction dataset. I developed the data-quality checks, PySpark transformations, customer-level scoring and segmentation, analysis tables, and a Databricks dashboard to translate transactional data into an actionable customer overview.

> **Data context:** This project analyzes a publicly available Kaggle e-commerce dataset for educational purposes. The analysis demonstrates an RFM customer segmentation workflow rather than reporting verified results for an operating retailer.

## Dashboard preview

[**View the RFM Customer Segmentation Dashboard (PDF)**](RFM Customer Segmentation Dashboard.pdf)

The original dashboard was built in **Databricks**. The PDF preserves its visual layout and results; it is a **static export**, not an interactive dashboard. The notebook contains the transformations used to prepare its source tables.

## Project results

| Metric | Result |
|---|---:|
| Source transaction rows | 286,392 |
| Completed/paid rows before additional filtering | 89,724 |
| Qualifying transaction rows after cleaning | 85,518 |
| Distinct qualifying orders | 61,258 |
| Customers included in RFM analysis | 29,695 |
| Qualifying revenue (dashboard, rounded) | $62.85 million |
| Average spend per qualifying customer (dashboard, rounded) | $2.12 thousand |

The dashboard summarizes **five customer segments**: Champions, Loyal Customers, Potential Loyalists, At Risk, and Lost Customers. It combines customer-segment distribution, monthly revenue, geographic revenue, and segment-level metrics, with dashboard filters for exploration.

## How the analysis works

1. **Import and inspect:** Connect to the transaction table in Databricks and examine its columns and sample records.
2. **Validate:** Check required fields, missing values, order statuses, and duplicate records.
3. **Filter qualifying transactions:** Retain `complete` and `paid` orders with valid key fields and positive amounts; document exclusions.
4. **Calculate RFM:** Aggregate transactions by customer. Recency is the number of days since the most recent qualifying order relative to the **latest transaction date in the analysis dataset**; Frequency counts distinct orders; Monetary sums qualifying transaction values.
5. **Score customers:** Use PySpark window functions and `ntile(5)` to assign R, F, and M scores from 1 to 5, with higher scores indicating more favourable customer activity.
6. **Segment:** Apply explicit, rule-based combinations of RFM scores to group customers into five business-oriented categories.
7. **Prepare reporting data:** Build customer-level and transaction-level reporting datasets, summarize revenue over time and by region, and save the dashboard tables in Databricks Delta format.
8. **Visualize:** Create a Databricks dashboard displaying overall KPIs, segment performance, monthly trends, and regional results.

### Customer segments

| Segment | Interpretation |
|---|---|
| **Champions** | Recent, frequent, high-spending customers |
| **Loyal Customers** | Customers with relatively strong recency and purchase frequency |
| **Potential Loyalists** | Recent customers with room to develop stronger purchasing habits |
| **At Risk** | Customers whose recency and frequency scores suggest declining engagement |
| **Lost Customers** | Customers with low recency and low frequency scores |

These are **rule-based analytical labels**, not known customer lifecycle outcomes. The notebook contains the exact conditions and precedence used to assign each label.

## Technologies and methods

- **Databricks:** notebook development, managed tables, Delta Lake output, and dashboard creation
- **PySpark:** DataFrame operations, cleaning, aggregation, joins, date handling, and window functions
- **RFM scoring:** `ntile(5)` quintiles and interpretable segmentation rules
- **Data quality:** missing-value checks, duplicate checks, status standardization, and auditable record exclusions
- **Reporting:** customer KPIs, regional summaries, monthly trends, and segment comparisons

## Data source and attribution

The project uses **Sales Data FY 2020–2021** published by **Nhi Yen (`nhiyen`)** on Kaggle:

- [Dataset used in this project — Sales Data FY 2020–2021](https://www.kaggle.com/datasets/nhiyen/sales-data-fy-2020-2021)

The downloaded dataset contains **286,392 rows and 36 columns**. The source CSV is available from the Kaggle links above and is not duplicated in this three-file repository.

## Repository contents

```text
.
├── README.md
├── Final_Project_Notebook_RFM_Segmentation.ipynb
└── RFM Customer Segmentation Dashboard.pdf
```

- [`Final_Project_Notebook_RFM_Segmentation.ipynb`](Final_Project_Notebook_RFM_Segmentation.ipynb) — source notebook for the RFM pipeline, scoring, segmentation, and dashboard datasets
- [`RFM Customer Segmentation Dashboard.pdf`](RFM Customer Segmentation Dashboard.pdf) — static view of the final Databricks dashboard

The notebook is the original final Databricks notebook export, including its saved execution outputs. The accompanying PDF shows the final Databricks dashboard.

## Reproducing the analysis

**Prerequisites:** A Databricks workspace with PySpark support; access to the dataset linked above; permissions to create or overwrite tables in your own schema.

1. Download the source dataset from Kaggle and upload it to a location accessible to your own Databricks workspace.
2. Import the CSV into a Databricks table. Check the CSV schema, date parsing, and numeric column types.
3. Import the `.ipynb` notebook into Databricks.
4. **Update the notebook's environment-specific table identifiers** before execution. In the original project, `SOURCE_TABLE` is set to `bia_5203.default.sales_06_fy_202021`, and dashboard outputs are written to tables under `bia_5203.default`. Substitute tables in your own workspace/schema.
5. Run notebook cells in order. Validate the qualifying row counts, customer counts, scores, and summary outputs against the reported results.
6. To recreate the visual dashboard, use the saved customer and transaction reporting tables in Databricks to configure the charts and filters shown in the PDF. **The PDF is not an importable dashboard definition**, so visualization layout must be recreated manually.

This notebook has not been independently rerun in a fresh Databricks workspace for this public release. Results can depend on source-file version, schema inference, and environment configuration.

## Interpretation and limitations

- The metrics reflect **qualifying completed/paid transactions** in the supplied sample; they are not a measure of all attempted orders.
- Recency depends on the analysis reference date and should be recalculated for new reporting periods.
- `ntile(5)` creates roughly equal-sized score groups, but equal values can fall across boundaries; scoring should be reassessed for production customer segmentation.
- The five segment names are descriptive heuristics, not independently validated predictions of future customer behaviour.
- The original Databricks dashboard is shown as a PDF; interactivity is available only in a separately configured Databricks environment.
- A production implementation could add automated quality checks, a scheduled refresh workflow, and evaluation of segmentation stability over time.
