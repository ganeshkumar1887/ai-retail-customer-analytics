# AI-Based Retail Customer Analytics, Data Warehouse & Churn Prediction

An end-to-end retail analytics and machine learning pipeline built in Python and SQLite. It transforms raw retail transaction logs into a validated star-schema data warehouse, performs multidimensional OLAP analysis and data cube generation, computes business KPIs, and predicts customer churn risk using Random Forest and Logistic Regression models.

---

## Key Features

- **Data Preprocessing & Quality Validation**
  - Cleans raw retail transactions (`541,909` original records → `524,878` cleaned records) by removing duplicates, cancelled invoices (`InvoiceNo` starting with `C`), and invalid quantities/unit prices.
  - Standardizes text fields, imputes product descriptions by `StockCode`, assigns `-1` to unidentified customers (`132,186` records), and engineers calendar attributes (`Year`, `Month`, `Quarter`, `MonthName`, `DayName`, `Hour`, `Season`).
  - Runs automated assertions and exports a quality summary (`outputs/data_quality_report.csv`).
- **Star Schema Data Warehousing (`SQLite`)**
  - Builds `outputs/retail_warehouse.db` with foreign-key enforcement:
    - **Fact Table:** `fact_sales` (`SalesID`, `InvoiceNo`, `CustomerID`, `ProductID`, `DateKey`, `CountryID`, `Quantity`, `UnitPrice`, `TotalSales`)
    - **Dimension Tables:** `dim_customer`, `dim_product`, `dim_date`, `dim_country`
- **OLAP Operations & Data Cube**
  - Executes SQL-based **Roll-up**, **Drill-down**, **Slice**, **Dice**, and pandas **Pivot** operations.
  - Generates a multidimensional sales cube (`outputs/sales_data_cube.csv`) aggregated by `Year`, `Quarter`, `Country`, and `StockCode`.
- **Customer Churn Modeling & Risk Scoring**
  - Engineers 7 customer behavioral/RFM features over a 180-day lookback window with a 60-day forward churn label:
    `Recency`, `Frequency`, `Monetary`, `Tenure`, `TotalQuantity`, `UniqueProducts`, `AvgOrderValue`.
  - Trains and evaluates **Random Forest** and **Logistic Regression** (`StandardScaler` pipeline) models with balanced class weights.
  - Scores customers into **Low** (`≤ 0.30`), **Medium** (`0.30–0.70`), and **High** (`≥ 0.70`) churn risk tiers and exports `outputs/customer_churn_predictions.csv` along with serialized `.pkl` models.

---

## Business KPIs & Results

### Summary KPIs (`outputs/business_kpis.csv`)

| Business Metric | Value |
| :--- | ---: |
| **Total Revenue (GBP)** | £10,642,110.80 |
| **Identified Customers** | 4,338 |
| **Total Orders** | 19,960 |
| **Total Products** | 3,922 |
| **Average Order Value (GBP)** | £533.17 |

### Data Quality Report (`outputs/data_quality_report.csv`)

| Quality Check | Result |
| :--- | ---: |
| Original Records | 541,909 |
| Cleaned Records | 524,878 |
| Duplicate Records | 0 |
| Missing Values | 0 |
| Invalid Quantity / Unit Price | 0 |
| Unknown Customer Records (`CustomerID = -1`) | 132,186 |
| Inconsistent Sales Calculation | 0 |

### Model Evaluation (`outputs/model_evaluation.csv`)

| Model | Accuracy | Precision | Recall | F1 Score | ROC AUC |
| :--- | ---: | ---: | ---: | ---: | ---: |
| **Random Forest** | 0.6568 | 0.6092 | 0.7716 | 0.6809 | 0.7146 |
| **Logistic Regression** | 0.6578 | 0.6065 | 0.7939 | 0.6877 | 0.7248 |

### Top Random Forest Feature Importances (`outputs/feature_importance.csv`)

1. `Monetary` — `0.1927`
2. `TotalQuantity` — `0.1762`
3. `Frequency` — `0.1612`
4. `Recency` — `0.1296`
5. `UniqueProducts` — `0.1291`
6. `AvgOrderValue` — `0.1124`
7. `Tenure` — `0.0989`

---

## Project Structure

```text
ai-retail-customer-analytics/
├── retail_customer_analytics.ipynb            # Main ETL, DWH, OLAP, EDA & Churn ML notebook
├── requirements.txt                           # Python dependencies
├── README.md
├── reports/
│   └── Retail_Customer_Analytics_Project_Report.pdf
└── outputs/
    ├── business_kpis.csv                      # High-level retail KPIs
    ├── cleaned_retail_data.csv                # Preprocessed transaction dataset
    ├── customer_churn_model.pkl               # Final trained Random Forest churn model
    ├── logistic_regression_model.pkl          # Trained Logistic Regression pipeline
    ├── customer_churn_predictions.csv         # Customer-level churn probabilities & risk tiers
    ├── data_quality_report.csv                # Data validation metrics
    ├── feature_importance.csv                 # Random Forest feature importance scores
    ├── model_evaluation.csv                   # Model comparison metrics
    ├── retail_warehouse.db                    # SQLite star-schema data warehouse
    ├── sales_data_cube.csv                    # Multidimensional OLAP data cube
    └── __results___files/                     # Generated EDA & model evaluation plots
```

---

## Tech Stack

- **Data Processing & Analysis:** Python, NumPy, pandas
- **Data Warehousing & OLAP:** SQLite (`sqlite3`)
- **Visualization:** Matplotlib, Seaborn
- **Machine Learning & Persistence:** scikit-learn (`RandomForestClassifier`, `LogisticRegression`, `StandardScaler`), `joblib`

---

## Getting Started

1. **Clone the repository**
   ```bash
   git clone https://github.com/ganeshkumar1887/ai-retail-customer-analytics.git
   cd ai-retail-customer-analytics
   ```

2. **Install Python dependencies**
   ```bash
   pip install numpy pandas matplotlib seaborn scikit-learn joblib jupyter
   ```

3. **Run the notebook**
   - Open `retail_customer_analytics.ipynb` in Jupyter Notebook / JupyterLab / VS Code.
   - Note: The notebook loads the raw retail `data.csv` from `/kaggle/input` and writes outputs to `/kaggle/working`. Update those `Path(...)` locations if running outside Kaggle.
   - Pre-generated outputs, SQLite warehouse, trained `.pkl` models, plots, and the PDF report are already included under `outputs/` and `reports/`.
