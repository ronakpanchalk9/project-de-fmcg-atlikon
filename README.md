# FMCG Analytics Lakehouse on Databricks

This project builds a Databricks data pipeline for integrating an FMCG child company's customer, product, pricing, and order data into a parent company's reporting model. It prepares curated monthly sales data and an enriched view for dashboard analysis.

## What the project does

- Creates an `fmcg` catalog with `bronze`, `silver`, and `gold` schemas, plus a monthly date dimension.
- Ingests customer, product, and gross-price CSVs into Delta tables. PySpark transformations remove duplicates, standardize names and categories, correct known data issues, parse dates, validate prices, and align records with the parent model.
- Processes orders through both full-load and incremental-load notebooks. Incremental files are staged and moved from landing to processed storage; Delta `MERGE` operations update or insert order records.
- Aggregates daily child-company orders to month, product, and customer grain, then merges them into the parent's Gold fact table. Dimension records are also merged into parent tables.
- Defines a SQL view joining sales to date, customer, product, and yearly price dimensions. It derives `total_amount_inr` from sold quantity and price; the resulting model feeds the FMCG dashboard in `2_dashboarding/fmcg_dashboard.pdf`.

## Repository layout

| Path | Purpose |
| --- | --- |
| `0_data/` | Parent-company full-load CSVs and incremental order data/query |
| `1_codes/1_setup/` | Catalog/schema setup, shared schema names, and date dimension |
| `1_codes/2_dimension_data_processing/` | Customer, product, and pricing pipelines |
| `1_codes/3_fact_data_processing/` | Full and incremental order fact pipelines |
| `2_dashboarding/` | Enriched fact SQL and dashboard PDF |
| `resources/` | Project architecture diagram and editable Excalidraw source |

## Processing flow

`CSV landing data → Bronze Delta → Silver cleanup/enrichment → Gold tables → parent-company MERGE → SQL reporting view → dashboard`

Run the setup notebooks first, then process dimensions (products before pricing and orders, because those pipelines join to product data). Run the fact full-load notebook for initial history and the incremental notebook for subsequent arrivals. The notebooks use Databricks features including Spark, Delta Lake, Unity Catalog-style catalog/schema naming, widgets, and `dbutils`; paths and table names are currently configured for the project's Databricks environment and should be adjusted for another workspace.

## Resume

**Databricks FMCG Sales Lakehouse | PySpark, SQL, Delta Lake**

- Built a Databricks medallion pipeline with PySpark to ingest and cleanse FMCG customer, product, pricing, and order data, applying deduplication, standardization, validation, and dimension enrichment into Delta tables.
- Implemented full and incremental fact processing with Delta Lake `MERGE`, monthly sales aggregation, and SQL reporting views that calculate INR sales value for dashboard analysis.
