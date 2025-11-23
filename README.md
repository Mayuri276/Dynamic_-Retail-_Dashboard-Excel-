# Dynamic Retail Dashboard (Excel Module)

> **Project:** Dynamic Retail Dashboard for Excel — Data Science Course
>
> **Author:** Mayuri Jamdar
>
> **Last updated:** 23 November 2025

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Data Description](#data-description)
3. [Repository Structure](#repository-structure)
4. [Prerequisites](#prerequisites)
5. [Setup & Installation](#setup--installation)
6. [Data Ingestion (from GitHub)](#data-ingestion-from-github)
7. [Data Processing & Cleaning Steps](#data-processing--cleaning-steps)
8. [Dashboard: Features & Visualizations](#dashboard-features--visualizations)
9. [How to Run (Step-by-step)](#how-to-run-step-by-step)
10. [Sample Table Schemas & Example Rows](#sample-table-schemas--example-rows)
11. [Common Issues & Troubleshooting](#common-issues--troubleshooting)
12. [Future Improvements](#future-improvements)
13. [Contributing](#contributing)
14. [License & Contact](#license--contact)

---

## Project Overview

This project builds a **dynamic retail dashboard** using Excel (and supporting Python scripts where applicable) as part of a Data Science course. The dashboard visualizes retail KPIs, trends, and granular metrics derived from three core tables: **orders**, **returns**, and **people**. The raw data is stored in CSV files and ingested from a GitHub repository.

**Goals:**

* Provide interactive/refreshable Excel dashboards for retail operations.
* Demonstrate ETL steps (ingestion → cleaning → feature engineering → visualization).
* Enable quick business insights: sales trends, return analysis, customer segmentation.

---

## Data Description

Three primary tables are used in this project:

1. **orders** — transactional sales data (order id, product id, date, qty, price, revenue, customer_id, store_id, etc.)
2. **returns** — information on returned items (return id, order id, return date, reason, refunded amount, etc.)
3. **people** — customer or employee demographic data (person id, name, email, phone, signup_date, location, customer_segment, etc.)

> The README includes sample schemas for each table (see section: Sample Table Schemas).

---

## Repository Structure

```
/ (root)
├── data/                     # raw and processed CSVs
│   ├── raw/
│   │   ├── orders.csv
│   │   ├── returns.csv
│   │   └── people.csv
│   └── processed/
├── notebooks/                # exploratory notebooks (optional)
├── scripts/                  # ETL & helper scripts
│   ├── ingest_from_github.py
│   ├── clean_data.py
│   └── feature_engineering.py
├── dashboard/                # Excel dashboard files
│   ├── Retail_Dashboard.xlsx
│   └── README-notes.md
├── requirements.txt          # Python dependencies (if any)
├── .gitignore
└── README.md                 # this file
```

---

## Prerequisites

* Microsoft Excel (recommended: Excel 2016 or later for full feature support)
* Python 3.8+ (only if using the scripts in `/scripts`)
* Packages (if using Python): listed in `requirements.txt` (e.g., pandas, openpyxl)
* Git & GitHub access (for ingestion from remote repo)

---

## Setup & Installation

1. Clone the repository:

```bash
git clone <your-repo-url>
cd <repo-folder>
```

2. (Optional) Create a Python virtual environment and install requirements:

```bash
python -m venv venv
source venv/bin/activate   # Linux / macOS
venv\Scripts\activate     # Windows
pip install -r requirements.txt
```

3. Open the Excel file in `dashboard/Retail_Dashboard.xlsx` and enable data connections/refresh if needed.

---

## Data Ingestion (from GitHub)

**Approach 1 — Manual download:**

* Navigate to the GitHub repo that contains the CSV files and use "Download" or raw file URL to save `orders.csv`, `returns.csv`, and `people.csv` into `data/raw/`.

**Approach 2 — Scripted ingestion (recommended for reproducibility):**

* Use `scripts/ingest_from_github.py` to download files programmatically. The script should:

  1. Read raw CSV URLs (or a configuration file with paths).
  2. Download CSVs into `data/raw/`.
  3. Verify checksum or row count (basic validation).

**Example command:**

```bash
python scripts/ingest_from_github.py --config config/ingest.yml
```

> The project is already configured to ingest data from GitHub. If you want, share the sample CSVs and I will give an ingestion script tailored to those URLs.

---

## Data Processing & Cleaning Steps

Follow these steps (each step should be implemented in `scripts/clean_data.py` or a notebook):

1. **Load raw CSVs** from `data/raw/`.
2. **Type conversions:** Ensure date columns parsed as `datetime`, numeric fields as `float/int`.
3. **Missing values:** Identify and handle missing fields. Strategies:

   * Drop rows with critical missing keys (order_id, person_id).
   * Impute numeric values using median or domain rules.
4. **Duplication:** Drop or reconcile duplicate rows (same order_id and timestamp).
5. **Join tables:** Create useful merged tables:

   * `orders` joined with `people` on `person_id` for customer-level metrics.
   * `orders` joined with `returns` on `order_id` to label returned orders.
6. **Feature engineering:** e.g., `order_total = qty * price`, `days_since_signup`, `is_returned` flag.
7. **Save processed data** to `data/processed/` as cleaned CSVs or Excel tables.

---

## Dashboard: Features & Visualizations

Recommended sheets and visuals inside the Excel dashboard:

* **Executive Summary (sheet):** KPIs — Total Revenue, Total Orders, Total Returns, Return Rate, AOV (Average Order Value), Active Customers.
* **Sales Trend (sheet):** Time series of daily/weekly/monthly revenue and order counts.
* **Returns Analysis (sheet):** Return rate by product/category/reason; top returned SKUs.
* **Customer Segmentation (sheet):** New vs returning customers, top customer cohorts, LTV approximation.
* **Geography / Store Performance (sheet):** Sales by location or store.
* **Top Products (sheet):** Top N products by revenue and units sold.
* **Pivot Tables & Slicers:** Allow filtering by date range, store, product category.

---

## How to Run (Step-by-step)

1. Ensure raw CSVs exist in `data/raw/`.
2. Run ingestion script (if using scripted ingestion):

```bash
python scripts/ingest_from_github.py
```

3. Run cleaning & feature engineering:

```bash
python scripts/clean_data.py
```

4. Open `dashboard/Retail_Dashboard.xlsx` and refresh data connections or import the processed CSVs.
5. Use slicers and pivot tables in Excel to interact with the dashboard.

---

## Sample Table Schemas & Example Rows

### orders (sample schema)

| Column Name | Type     | Description             |
| ----------- | -------- | ----------------------- |
| order_id    | string   | Unique order identifier |
| order_date  | datetime | Order timestamp         |
| person_id   | string   | Customer identifier     |
| product_id  | string   | Product identifier      |
| qty         | integer  | Quantity ordered        |
| unit_price  | float    | Unit sale price         |
| revenue     | float    | qty * unit_price        |
| store_id    | string   | Store or channel        |

**Example row:** `O12345, 2025-03-12, P987, PR345, 2, 499.00, 998.00, S01`

### returns (sample schema)

| Column Name     | Type     | Description             |
| --------------- | -------- | ----------------------- |
| return_id       | string   | Unique return id        |
| order_id        | string   | Original order id       |
| return_date     | datetime | Return timestamp        |
| reason          | string   | Return reason code/text |
| refunded_amount | float    | Amount refunded         |

**Example row:** `R444, O12345, 2025-03-18, Damaged, 998.00`

### people (sample schema)

| Column Name | Type     | Description                  |
| ----------- | -------- | ---------------------------- |
| person_id   | string   | Unique person id             |
| name        | string   | Customer name                |
| email       | string   | Contact email                |
| signup_date | datetime | Date of first interaction    |
| segment     | string   | e.g., Retail, Wholesale, VIP |

**Example row:** `P987, Mayuri Jamdar, mayuri@example.com, 2024-11-01, Retail`

---

## Common Issues & Troubleshooting

* **Date parsing errors:** Ensure consistent date formats; use `dayfirst` or format string when parsing.
* **Large Excel file size:** Save processed data as CSV and use data connections rather than embedding large tables.
* **Pivot table not refreshing:** Make sure to refresh data sources and check table ranges.

---

## Future Improvements

* Automate ingestion on a schedule (GitHub → Azure/AWS → Excel or Power BI).
* Add more detailed customer lifetime value (LTV) modeling.
* Migrate dashboard to Power BI or Streamlit for better interactivity.
* Add unit tests for ETL scripts.

---

## Contributing

If you'd like to contribute:

1. Fork the repo
2. Create a feature branch
3. Submit a PR with description and tests (if applicable)

---

## License & Contact

This project is licensed under the MIT License — adapt as needed.

If you have questions or want the README modified, please tell me which sections to change or share the sample CSVs and I will update the README with tailored ingestion and cleaning commands.



1)
    <img width="362" height="299" alt="image" src="https://github.com/user-attachments/assets/80f222e9-bfb0-4ecd-9990-713f8c12a7e3" />

2)
   <img width="1429" height="620" alt="image" src="https://github.com/user-attachments/assets/7427fb37-d29c-48e7-9aa3-fd897335508b" />

3)
<img width="322" height="191" alt="image" src="https://github.com/user-attachments/assets/bff2626b-834c-4c3e-aedd-8e44c97de9d2" />

  4)
  <img width="255" height="209" alt="image" src="https://github.com/user-attachments/assets/3184c695-56cf-421f-a446-133ff83bf782" />


