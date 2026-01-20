# Transaction Data Pipeline & Validation (Customer–Month Data Mart)

## Overview
This project demonstrates an end-to-end **batch-style data pipeline** that converts **raw transaction events** into a **clean, validated customer–month dataset** that can be safely used for analytics and downstream machine learning.

The emphasis is on **data engineering quality**:
- correct aggregation grain
- reproducible transformations
- integrity checks (reconciliation)
- outlier handling with clear rationale
- publishable output dataset

## Why this project
In real organisations, most value comes from **reliable datasets** rather than complex models. This project shows how to:
- take messy event-level data (transactions)
- build a curated “analytics-ready” table
- prove it’s correct using statistical and integrity checks

## Dataset
Public retail transaction dataset: **dunnhumby – The Complete Journey** (multiple CSV tables).

Key raw files used:
- `transaction_data.csv` — transaction events (row-level purchases)
- `product.csv` — product metadata
- `hh_demographic.csv` — household attributes  
(Other files like coupons/campaigns can be used for extension.)

## Output
A curated customer-month dataset is produced:

- **File:** `customer_month_pipeline_output.csv`
- **Grain:** one row per `(household_key, month_index)`
- **Purpose:** stable analytics table for reporting, segmentation, forecasting, and anomaly detection extensions

### Output columns (core)
- `household_key` — customer/household identifier
- `month_index` — month bucket derived from the dataset’s `DAY` field
- `total_spend` — total spend per household per month
- `total_spend_capped` — 99th percentile capped spend (robust to extreme values)
- `txns` — number of shopping trips (unique `basket_id`)
- `line_items` — number of purchased line items (`product_id` count)
- `avg_line_value` — average line-level spend
- `median_line_value` — median line-level spend (robust statistic)

## Pipeline steps
1. **Ingest raw data**  
   Load raw CSVs as untrusted event data.

2. **Profile & validate schema**  
   Inspect row counts, column types, missingness, and key fields.

3. **Transform to analytical grain (customer–month)**  
   Aggregate transaction events to a stable grain using deterministic groupby logic.

4. **Integrity reconciliation (critical)**  
   Verify monetary preservation:
   - total spend before aggregation equals total spend after aggregation  
   This confirms the pipeline didn’t lose or duplicate value.

5. **Distribution checks & robust outlier handling**
   - analyse spend distribution (skew/long tail)
   - apply **99th percentile capping** to reduce extreme influence while preserving behaviour

6. **Data quality gates**
   - no missing values in engineered fields
   - no negative spend rows (domain check)
   - no zero-transaction months in final table

7. **Publish output**
   Write curated table to `customer_month_pipeline_output.csv`.

## How to run
### Requirements
- Python 3.10+
- pandas

### Run (notebook)
1. Place raw CSVs in your data folder (or adjust paths):
   - `transaction_data.csv`,_


