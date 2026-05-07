# 🛍️ Customer Shopping Behavior Analysis — Capstone Project

An end-to-end data analysis capstone project combining Python, SQL, and Power BI to analyse customer shopping behavior across 3,900 transactions — covering data wrangling, feature engineering, PostgreSQL integration, business-driven SQL analysis, and an interactive dashboard.

---

## 📌 Short Description / Purpose

This project analyses customer shopping behavior using transactional data from 3,900 purchases across various product categories. The goal is to uncover insights into spending patterns, customer segments, product preferences, and subscription behavior to guide strategic business decisions.

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| **Python (pandas)** | Data loading, cleaning, feature engineering, and PostgreSQL export |
| **PostgreSQL** | Structured business analysis using advanced SQL queries |
| **SQLAlchemy** | Python-to-PostgreSQL database connection and data loading |
| **Power BI** | Interactive dashboard for visual insights |
| **Jupyter Notebook** | Python development environment |

---

## 📂 Data Source

**Dataset:** `customer_shopping_behavior.csv`
**Rows:** 3,900 | **Columns:** 18 | **Missing Data:** 37 values in `Review Rating`

| Column | Description |
|--------|-------------|
| `Customer ID` | Unique customer identifier |
| `Age` | Customer age |
| `Gender` | Male / Female |
| `Item Purchased` | Name of item bought (25 unique items) |
| `Category` | Clothing / Footwear / Outerwear / Accessories |
| `Purchase Amount (USD)` | Transaction value ($20–$100) |
| `Location` | US state (all 50 states) |
| `Size` | S / M / L / XL |
| `Color` | Product colour (25 unique) |
| `Season` | Winter / Spring / Summer / Fall |
| `Review Rating` | Customer rating (2.5–5.0) |
| `Subscription Status` | Yes / No |
| `Shipping Type` | Express / Free Shipping / Next Day Air / Standard / 2-Day / Store Pickup |
| `Discount Applied` | Yes / No |
| `Promo Code Used` | Yes / No |
| `Previous Purchases` | Number of past purchases |
| `Payment Method` | Venmo / Cash / Credit Card / PayPal / Bank Transfer |
| `Frequency of Purchases` | Weekly / Fortnightly / Bi-Weekly / Monthly / Quarterly / Annually |

---

## 📁 Project Structure

```
customer-shopping-behavior/
│
├── customer_shopping_behavior.csv           # Raw dataset
├── customer_behavior_analysis.ipynb         # Python — data wrangling & PostgreSQL export
├── Capstone_Project_Python_SQL_Power_BI.sql # SQL — 10 business analysis queries
├── Customer_Behavior_Dashboard.pbix         # Power BI dashboard file
├── Customer_Shopping_Behavior_Analysis.pdf  # Full project report
├── Snapshot_of_Customer_Behavior_Dashboard.png  # Dashboard preview
└── README.md
```

---

## 🐍 Part 1: Python — Data Wrangling & Feature Engineering

**File:** `customer_behavior_analysis.ipynb`

### Step 1 — Load & Explore
- Imported dataset using `pandas`
- Used `df.info()`, `df.describe()`, `df.head()` / `df.tail()` for initial exploration
- Checked column names, data types, and summary statistics

### Step 2 — Missing Value Handling
- Identified 37 null values in the `Review Rating` column
- Imputed using **per-category median** rating — preserves row count and respects category-level distributions:
```python
df['Review Rating'] = df.groupby('Category')['Review Rating'].transform(
    lambda x: x.fillna(x.median())
)
```

### Step 3 — Duplicate Check
- Verified no duplicate rows exist in the dataset

### Step 4 — Column Standardisation
- Converted all column names to `snake_case`
- Renamed `purchase_amount_(usd)` → `purchase_amount` to remove special characters

### Step 5 — Feature Engineering
- **`age_group`** — binned customer ages into 4 quartile-based groups using `pd.qcut`:
  - Young Adult · Adult · Middle-aged · Senior
- **`purchase_frequency_days`** — mapped categorical frequency labels to numeric day intervals:
  - Weekly=7, Fortnightly/Bi-Weekly=14, Monthly=30, Quarterly/Every 3 Months=90, Annually=365

### Step 6 — Redundant Column Removal
- Verified `discount_applied` and `promo_code_used` were **identical across all rows**
- Dropped `promo_code_used` to eliminate redundancy

### Step 7 — PostgreSQL Export
- Connected to local PostgreSQL using `SQLAlchemy` + `psycopg2`
- Loaded cleaned DataFrame into `customer` table in `customer_behavior` database

---

## 🗄️ Part 2: SQL — Business Analysis (PostgreSQL)

**File:** `Capstone_Project_Python_SQL_Power_BI.sql`

10 business questions answered using PostgreSQL:

| # | Question | Key Finding | SQL Concept |
|---|---------|------------|-------------|
| Q1 | Revenue by Gender | Male: $157,890 \| Female: $75,191 | `GROUP BY`, `SUM` |
| Q2 | High-spending discount users | 839 customers used discounts but spent above average | Subquery, `AVG` |
| Q3 | Top 5 products by rating | Gloves (3.86), Sandals (3.84), Boots (3.82) | `ROUND`, `AVG`, `ORDER BY` |
| Q4 | Shipping type comparison | Express ($60.48) vs Standard ($58.46) | `WHERE IN`, `AVG` |
| Q5 | Subscribers vs Non-subscribers | 1,053 subscribers \| 2,847 non-subscribers | `GROUP BY`, `COUNT`, `SUM` |
| Q6 | Discount-dependent products | Hat (50%), Sneakers (49.66%), Coat (49.07%) | `CASE WHEN`, percentage calculation |
| Q7 | Customer segmentation | Loyal: 3,116 \| Returning: 701 \| New: 83 | `CTE`, `CASE WHEN` |
| Q8 | Top 3 products per category | Jewelry, Blouse, Sandals, Jacket lead each category | `CTE`, `ROW_NUMBER()` window function |
| Q9 | Repeat buyers & subscriptions | 2,518 repeat buyers not subscribed vs 958 subscribed | `WHERE`, `GROUP BY` |
| Q10 | Revenue by age group | Young Adult: $62,143 \| Middle-aged: $59,197 | `GROUP BY`, `SUM`, `ORDER BY` |

---

## 📊 Part 3: Power BI Dashboard

**File:** `Customer_Behavior_Dashboard.pbix`

### Key KPIs
| Metric | Value |
|--------|-------|
| Number of Customers | 3.9K |
| Average Purchase Amount | $59.76 |
| Average Review Rating | 3.75 / 5 |

### Visuals
- **% of Customers by Subscription Status** — Donut chart (Yes: 27% \| No: 73%)
- **Revenue by Category** — Bar chart (Clothing leads at ~$100K)
- **Sales by Category** — Bar chart (Clothing: 1,737 \| Accessories: 1,240)
- **Revenue by Age Group** — Horizontal bar (Young Adult leads at ~$62K)
- **Sales by Age Group** — Horizontal bar (Young Adult leads with ~950 sales)

### Slicers
- Subscription Status (Yes / No)
- Gender (Female / Male)
- Category (Accessories / Clothing / Footwear / Outerwear)
- Shipping Status (6 options)

---

## 💡 Business Recommendations

| Insight | Recommended Action |
|---------|-------------------|
| 💳 Only 27% of customers are subscribed | Promote exclusive subscriber benefits to grow the base |
| 🔁 3,116 loyal customers vs only 83 new customers | Launch a loyalty rewards programme to retain the loyal base |
| 🏷️ Hat & Sneakers have ~50% discount rate | Review discount policy — balance sales boost with margin control |
| ⭐ Gloves & Sandals have the highest ratings | Feature top-rated products prominently in marketing campaigns |
| 👶 Young Adults drive the highest revenue ($62K) | Focus targeted marketing on the Young Adult segment |
| 🚀 Express shipping users spend more ($60.48) | Promote express shipping upsells at checkout |

---

## 🛠️ How to Run

### Python
1. Install dependencies: `pip install pandas sqlalchemy psycopg2-binary`
2. Open `customer_behavior_analysis.ipynb` in Jupyter Notebook
3. Run all cells in order
4. Update PostgreSQL credentials in Step 10 before running the export cell

### SQL
1. Ensure the Python notebook has been run and data is loaded into PostgreSQL
2. Open `Capstone_Project_Python_SQL_Power_BI.sql` in pgAdmin or any PostgreSQL client
3. Run queries individually or all at once

### Power BI
1. Open `Customer_Behavior_Dashboard.pbix` in Power BI Desktop
2. Use the slicers to filter by Subscription Status, Gender, Category, or Shipping Type

> ⚠️ Do not commit your PostgreSQL credentials to GitHub. Update the username/password in the notebook before sharing.

---

## 💡 Key Concepts Used

| Concept | Applied In |
|---------|-----------|
| `groupby().transform()` | Per-category median imputation for Review Rating |
| `pd.qcut` | Quartile-based age group binning |
| Dictionary mapping | Frequency label → numeric days conversion |
| Column redundancy check | `discount_applied` vs `promo_code_used` comparison |
| SQLAlchemy engine | Python-to-PostgreSQL data pipeline |
| Subquery | High-spending discount users (Q2) |
| `CASE WHEN` | Customer segmentation (Q7) and discount rate (Q6) |
| CTE (`WITH`) | Customer segmentation (Q7) and top products per category (Q8) |
| `ROW_NUMBER()` window function | Ranking top 3 products within each category (Q8) |
| `ROUND(AVG(...))` | Clean aggregated output throughout |
| Power BI slicers | Interactive multi-filter dashboard |
