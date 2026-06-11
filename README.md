# 📊 Task 4: Data Cleaning & Reporting Automation

> **Internship Project** | Python · Pandas · NumPy · Matplotlib · Seaborn · OpenPyXL

---

## 🎯 Project Overview

This project automates the full data pipeline for a sales dataset — from raw, messy CSV to a polished Excel report with embedded charts — using only Python. No manual Excel work required.

### What it does:
1. Generates a realistic `sales_data.csv` with 1,030 records (including intentional data quality issues)
2. Cleans the data: handles missing values, duplicates, outliers, and inconsistent text
3. Validates all columns (correct types, non-negative values, valid dates)
4. Computes summary statistics and KPIs
5. Produces 4 professional-quality charts (saved as PNG)
6. Exports a clean CSV and a 3-sheet Excel report

---

## 📁 Project Structure

```
internship_project/
│
├── Data_Cleaning_Automation.ipynb   # Main analysis notebook (step-by-step)
│
├── sales_data.csv                   # Raw dataset (1,030 rows with issues)
├── cleaned_sales_data.csv           # Cleaned dataset (1,000 rows)
├── automation_report.xlsx           # Excel report (3 sheets)
│
├── charts/
│   ├── chart1_sales_by_category.png
│   ├── chart2_monthly_sales_trend.png
│   ├── chart3_top10_products.png
│   └── chart4_citywise_sales.png
│
├── README.md                        # This file
└── Project_Report.md                # Detailed 6-page project report
```

---

## 🚀 Quick Start

### Prerequisites
Make sure you have Python 3.8+ installed.

### 1. Clone / Download the Repository
```bash
git clone https://github.com/YOUR_USERNAME/data-cleaning-automation.git
cd data-cleaning-automation
```

### 2. Install Dependencies
```bash
pip install pandas numpy matplotlib seaborn openpyxl jupyter
```

### 3. Run the Notebook
```bash
jupyter notebook Data_Cleaning_Automation.ipynb
```
Run all cells top-to-bottom (Cell → Run All). Outputs appear after each step.

---

## 📋 Step-by-Step Workflow

| Step | Task | Method |
|------|------|--------|
| 0 | Import libraries | `import pandas as pd` etc. |
| 1 | Load dataset | `pd.read_csv()` |
| 2 | Handle missing values | `fillna()` with mode/median/constant |
| 3 | Remove duplicates | `drop_duplicates()` |
| 4 | Standardise text | `.str.lower().replace(map).str.title()` |
| 5 | Detect & cap outliers | IQR method with `.clip()` |
| 6 | Validate data | Type casting, range checks |
| 7 | Summary statistics | `groupby().agg()` |
| 8 | Generate charts | Matplotlib + Seaborn |
| 9 | Export cleaned CSV | `df.to_csv()` |
| 10 | Build Excel report | OpenPyXL with formatting |

---

## 📊 Dataset Details

| Column | Type | Description |
|--------|------|-------------|
| Order_ID | String | Unique order identifier (ORD-0001 … ORD-1000) |
| Customer_Name | String | Full name (First + Last) |
| Product | String | Product name (intentionally inconsistent case) |
| Category | String | Electronics / Clothing / Food / Books / Home Decor |
| Quantity | Integer | Units ordered (1–10, with injected outliers) |
| Price | Float | Unit price in ₹ (with injected extreme values) |
| Order_Date | Date | YYYY-MM-DD format, range 2023-01-01 to 2023-12-31 |
| City | String | 10 Indian cities (with spelling variants, wrong case) |

### Intentional Data Quality Issues
- **~15% missing values** spread across 6 columns
- **30 duplicate records** injected
- **City names** with 4–6 variants each (e.g. `"MUMBAI"`, `"mumabi"`, `"pink city"`)
- **Product names** in random lower/upper/title case with trailing spaces
- **Price outliers**: 15 records with 10–15× the expected price
- **Quantity outliers**: 8 records with values 50–200 (normal range: 1–10)

---

## 📈 Key Results

| Metric | Value |
|--------|-------|
| Raw records | 1,030 |
| After cleaning | 1,000 |
| Missing values resolved | 156 |
| Duplicates removed | 30 |
| Total revenue | ₹8.09M |
| Top category | Electronics |
| Best city | Jaipur |

---

## 🛠️ Libraries Used

| Library | Version | Purpose |
|---------|---------|---------|
| pandas | ≥1.5 | Data loading, cleaning, aggregation |
| numpy | ≥1.23 | Numerical operations |
| matplotlib | ≥3.6 | Chart plotting |
| seaborn | ≥0.12 | Statistical visualisation styling |
| openpyxl | ≥3.0 | Excel file generation with formatting |

---

## 📤 GitHub Upload Instructions

```bash
# 1. Initialise git repository
git init

# 2. Add all project files
git add .

# 3. Commit
git commit -m "Initial commit: Task 4 Data Cleaning & Reporting Automation"

# 4. Create repo on GitHub (github.com → New Repository)
#    Name: data-cleaning-automation
#    Visibility: Public

# 5. Connect and push
git remote add origin https://github.com/YOUR_USERNAME/data-cleaning-automation.git
git branch -M main
git push -u origin main
```

### Recommended `.gitignore`
```
__pycache__/
*.pyc
.ipynb_checkpoints/
.DS_Store
```

---

## 👨‍💻 Author

Internship Project — Task 4
Python Data Analyst Intern

---

*Generated with Python · Pandas · Matplotlib · Seaborn · OpenPyXL*
