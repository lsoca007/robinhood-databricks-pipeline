# Robinhood Databricks Pipeline

A medallion pipeline (Bronze → Silver → Gold) built on Databricks and Delta Lake to parse and analyze personal Robinhood 1099 tax data using PySpark.

## Overview

Robinhood's consolidated 1099 tax export is a mixed-format CSV containing multiple record types with embedded header rows. This pipeline isolates the 1099-B trade records, cleans and types the data across medallion layers, and produces a gold-level profit/loss summary broken down by short-term and long-term trades.

## Pipeline Architecture

```
Raw CSV → Bronze → Silver → Gold (Trades) → Gold (Summary)
```

| Layer | Table | Description |
|-------|-------|-------------|
| Bronze | `bronze_raw` | Raw CSV ingested as-is into Delta |
| Silver | `silver_trades` | 1099-B rows isolated, null-filtered, and snake_case normalized |
| Gold | `gold_trades` | Typed columns with calculated `profit_loss` per trade |
| Gold | `gold_summary` | Profit/loss aggregated by term (short-term vs long-term) |

## Key Technical Challenges

- **Mixed-format CSV parsing** — the source file contains multiple record types with embedded header rows; 1099-B rows are isolated by prefix matching before DataFrame creation
- **Pre-rename null filtering** — data quality checks run against original column names before `toDF()` rename to avoid unresolved column errors
- **Schema metadata mismatch handling** — gold tables are dropped and recreated on schema changes using `DROP TABLE IF EXISTS`
- **Built-in shadowing prevention** — PySpark's `sum` is aliased as `spark_sum` to avoid overwriting Python's built-in

## Tech Stack

- **Platform:** Databricks (Unity Catalog)
- **Storage:** Delta Lake
- **Language:** PySpark (Python)
- **Architecture:** Medallion (Bronze / Silver / Gold)

## Data Source

Source data is a personal Robinhood 1099 tax file. Raw data is not included in this repo.

## Repository Structure

```
robinhood-databricks-pipeline/
├── robinhood_medallion_pipeline.ipynb   # Full pipeline notebook
└── README.md
```
