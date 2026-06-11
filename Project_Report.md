# Project Report
## Task 4: Data Cleaning & Reporting Automation

---

**Project Title:** Automated Data Cleaning and Report Generation using Python  
**Technology Stack:** Python 3 · Pandas · NumPy · Matplotlib · Seaborn · OpenPyXL  
**Dataset:** `sales_data.csv` — 1,030 records · 8 columns  
**Deliverable Date:** 2024

---

## 1. Introduction

### 1.1 Background

Data is rarely clean in the real world. Before any business decision can be made from data, analysts must spend a significant portion of their time — often cited as 60–80% — cleaning, transforming, and validating it. Doing this manually in tools like Excel is slow, error-prone, and cannot scale to large datasets.

This project addresses that challenge by building a **fully automated data cleaning and reporting pipeline** in Python. The pipeline takes a raw, messy CSV file as input and produces a clean dataset, visualisations, and a professional Excel report as output — without any manual intervention.

### 1.2 Objectives

- Demonstrate practical skills in data cleaning using Pandas
- Implement industry-standard techniques for handling missing values, duplicates, and outliers
- Produce publication-quality charts that communicate business insights clearly
- Automate Excel report generation with OpenPyXL, eliminating manual formatting effort
- Create well-documented, beginner-friendly code suitable for a professional portfolio

### 1.3 Scope

The project covers the complete data lifecycle from ingestion through reporting for a fictional e-commerce sales dataset covering one calendar year (2023) across 10 Indian cities, 5 product categories, and 50 products.

---

## 2. Dataset Description

### 2.1 Raw Dataset — `sales_data.csv`

The dataset was synthetically generated to simulate a realistic operational sales database. It contains **1,030 records** (1,000 genuine + 30 injected duplicates) with the following schema:

| Column | Data Type | Description | Sample Values |
|--------|-----------|-------------|---------------|
| Order_ID | String | Unique order identifier | ORD-0001, ORD-0500 |
| Customer_Name | String | Customer full name | Priya Sharma, Rahul Patel |
| Product | String | Product name | Laptop, T-Shirt, Coffee |
| Category | String | Product category | Electronics, Clothing, Food |
| Quantity | Integer | Units in order | 1–10 (outliers up to 200) |
| Price | Float | Unit price in ₹ | ₹50–₹60,000 |
| Order_Date | String | Order date (YYYY-MM-DD) | 2023-01-01 to 2023-12-31 |
| City | String | Customer city | Mumbai, Delhi, Bangalore… |

### 2.2 Intentional Data Quality Issues

The following issues were deliberately injected to make the dataset realistic:

**Missing Values (156 total):**
- Customer_Name: ~3% missing → filled with "Unknown Customer"
- Product: ~2.5% missing → filled with mode (most common product)
- Category: ~2% missing → filled with mode
- Quantity: ~3% missing → filled with median
- Price: ~2.5% missing → filled with median
- City: ~2% missing → filled with "Unknown City"

**Duplicate Records:**
- 30 exact duplicate rows injected (randomly selected existing rows re-appended)

**Inconsistent Text Formats — City Names:**
Each of the 10 cities had 4–6 variants:
- Mumbai: `"mumbai"`, `"MUMBAI"`, `"Mumabi"`, `"MumbAI"`, `"Mumbai "`
- Bangalore: `"Bangalore"`, `"bengaluru"`, `"Banglore"`, `"BANGALORE"`
- Jaipur: `"jaipur"`, `"JAIPUR"`, `"Pink City"`, `"pink city"`
- And so on for all 10 cities

**Outliers:**
- Price: 15 records with 10×–15× their expected price (e.g. ₹550,000 for a T-shirt)
- Quantity: 8 records with values between 50 and 200 (normal range: 1–10)

---

## 3. Methodology

### 3.1 Step-by-Step Cleaning Process

#### Step 1: Data Loading and Inspection

```python
df_raw = pd.read_csv('sales_data.csv')
print(df_raw.shape)      # (1030, 8)
print(df_raw.dtypes)
df_raw.describe()
```

Initial inspection revealed the shape, data types, and basic statistics. Price and Quantity were stored as floats due to missing values preventing integer inference.

#### Step 2: Missing Value Treatment

The strategy varied by column type:

- **Placeholder strings** for identity columns (Customer_Name, City) where statistical imputation would be meaningless
- **Mode imputation** for categorical columns (Product, Category), preserving the most common value
- **Median imputation** for numeric columns (Quantity, Price), which is robust to outliers unlike mean imputation

```python
df['Quantity'] = df['Quantity'].fillna(df['Quantity'].median())
df['Price'] = df['Price'].fillna(df['Price'].median())
```

All 156 missing values were resolved with zero remaining nulls.

#### Step 3: Duplicate Removal

Pandas' `drop_duplicates()` function removed all 30 duplicate rows, reducing the dataset from 1,030 to 1,000 records. The `.reset_index(drop=True)` call ensured a clean, contiguous index.

#### Step 4: Text Standardisation

A two-step process was used for city names:
1. Lowercase and strip whitespace: `.str.strip().str.lower()`
2. Apply a lookup dictionary: `.replace(city_map)`

Product and Category names received simpler treatment: `.str.strip().str.title()` to ensure consistent Title Case.

This approach is preferred over regex substitution because it is explicit, readable, and easy to extend.

#### Step 5: Outlier Detection and Capping

The IQR (Interquartile Range) method was chosen over Z-score because it is non-parametric — it makes no assumptions about the data following a normal distribution.

```
Lower Bound = Q1 − 1.5 × IQR
Upper Bound = Q3 + 1.5 × IQR
```

Values outside this range were **capped** (Winsorised) rather than dropped. This preserves the record count while limiting distortion from extreme values. A total of 23 values were capped across Price and Quantity.

#### Step 6: Data Validation

Final checks confirmed:
- All dates parseable as `datetime64`
- No negative prices or quantities after absolute value application
- Integer casting successful for Quantity

#### Step 7: Feature Engineering

Two derived columns were added:
- `Sales = Price × Quantity` — the primary business metric
- `Month = Order_Date.dt.to_period('M')` — for temporal aggregation

### 3.2 Reporting Pipeline

#### Summary Statistics

Group-level aggregations were computed using `groupby().agg()`:
- Revenue, order count, and average order value by Category
- Revenue ranking by City and by Product

#### Visualisations

Four charts were produced to cover different analytical dimensions:

| Chart | Type | Insight |
|-------|------|---------|
| Sales by Category | Vertical Bar | Which categories drive the most revenue |
| Monthly Sales Trend | Line + Area | Seasonality and month-over-month patterns |
| Top 10 Products | Horizontal Bar | Highest-revenue individual products |
| City-wise Distribution | Pie + Bar combo | Geographic revenue concentration |

All charts use a consistent colour palette, custom title formatting, and data labels for immediate readability.

#### Excel Report

The `automation_report.xlsx` file was built programmatically with OpenPyXL and contains three sheets:

**Sheet 1 — Summary:**
- Header banner with project title
- KPI card row (Total Revenue, Orders, Avg Order Value, Top Category)
- Data Cleaning Summary table (before/after metrics)
- Revenue by Category breakdown table

**Sheet 2 — Cleaned Data:**
- Full 1,000-row cleaned dataset
- Alternating row fill colours for readability
- Freeze panes at row 3
- Auto-filter on all column headers

**Sheet 3 — Charts:**
- All 4 visualisation images embedded at fixed dimensions
- Descriptive labels above each image
- Dark header banner

---

## 4. Results and Findings

### 4.1 Data Quality Summary

| Issue | Before | After |
|-------|--------|-------|
| Total rows | 1,030 | 1,000 |
| Missing values | 156 | 0 |
| Duplicate rows | 30 | 0 |
| Price outliers | 15 | 0 (capped) |
| Quantity outliers | 8 | 0 (capped) |
| City name variants | 29 | 11 (10 cities + 1 unknown) |

### 4.2 Business Insights

**Revenue:**
- Total annual revenue: ₹8.09 million
- Average order value: ₹8,090
- Electronics leads all categories by a significant margin, followed by Clothing

**Geography:**
- Revenue is broadly distributed across all 10 cities, indicating healthy geographic diversification
- No single city accounts for more than ~15% of total revenue

**Products:**
- High-value electronics (Laptop, Camera, Monitor) dominate the top-10 product list
- Within non-electronics categories, Saree and Rug represent the highest-value items

**Seasonality:**
- Monthly sales show moderate variation throughout 2023
- No extreme seasonal spikes, suggesting consistent year-round demand

---

## 5. Technical Challenges and Solutions

### Challenge 1: City Name Normalisation with NaN Values

**Problem:** After filling missing City values with "Unknown", the `.map(lambda x: x.title())` call failed because pandas had propagated `float('nan')` values through the chain before the map was applied.

**Solution:** Used `.replace(city_map)` (a dictionary-based vectorised operation) instead of `.map()` with a lambda. Then applied a conditional `.apply()` only for values not already in the known-cities set, avoiding any lambda calls on NaN.

### Challenge 2: Outlier Handling Strategy

**Problem:** Simply dropping outlier rows would reduce dataset size and potentially remove valid (if extreme) orders.

**Solution:** Implemented Winsorisation — capping values at the IQR bounds rather than removing rows. This preserves record count while eliminating distortion. The function was made reusable and reports the number of values affected.

### Challenge 3: Excel Report Scalability

**Problem:** The Cleaned Data sheet needed to handle 1,000 rows with formatting applied to every cell, which risked being slow.

**Solution:** Used a single loop with `itertuples()` (the fastest Pandas iteration method) and applied alternating fill colours using the row index modulo 2, rather than applying styles post-hoc with multiple passes.

---

## 6. Skills Demonstrated

| Skill | Tool / Technique |
|-------|-----------------|
| Data ingestion | `pd.read_csv()`, dtype inspection |
| Missing value handling | `fillna()` with mode, median, constants |
| Duplicate detection | `drop_duplicates()`, `duplicated().sum()` |
| String normalisation | `.str.strip()`, `.str.lower()`, `.replace()`, `.str.title()` |
| Outlier detection | IQR method, `quantile()`, `clip()` |
| Data validation | `pd.to_datetime()`, `pd.to_numeric()`, `.abs()` |
| Feature engineering | Derived columns, `dt.to_period()` |
| Aggregation | `groupby()`, `agg()`, `nlargest()` |
| Data visualisation | Bar, line, area, horizontal bar, pie charts |
| Excel automation | OpenPyXL workbook, sheet, cell styling, images |
| Code documentation | Inline comments, docstrings, markdown cells |

---

## 7. Conclusion

This project successfully demonstrates a complete, automated data cleaning and reporting pipeline that could be applied to any tabular sales dataset with minimal modification.

The pipeline reduces what would typically be several hours of manual Excel work to a single Python script execution. It is reproducible (same inputs always produce identical outputs), scalable (handles any number of rows without code changes), and maintainable (each cleaning step is modular and independently testable).

### Future Enhancements

- **Scheduling**: Deploy with Apache Airflow or cron to run automatically on new data drops
- **Database integration**: Read directly from PostgreSQL / MySQL instead of CSV
- **Email delivery**: Send the Excel report as an attachment via Python's `smtplib`
- **Interactive dashboard**: Convert the static charts to a Plotly Dash or Streamlit web app
- **Anomaly detection**: Add ML-based outlier detection (Isolation Forest) for more sophisticated flagging

---

*End of Report*

---

**Word count:** ~1,800 words | **Pages:** ~6 | **Diagrams:** 4 charts + tables

*Project completed as part of Python Data Analysis Internship — Task 4*
