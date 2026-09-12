
Retail Sales Performance Analysis

An end-to-end data analyst project: cleaning a genuinely messy retail transactions dataset, answering real business questions using both Python and SQL, and presenting the findings in an interactive Power BI dashboard.

Business problem

A retail store has 3 years of transaction data (2022–2024) but no clear view of what's actually driving performance — which categories sell best, whether online or in-store is stronger, whether discounting is working, and where the outliers are. This project cleans the raw data and answers those questions with evidence, not assumptions.

Dataset
Source: Retail Store Sales — Dirty for Data Cleaning (Kaggle, synthetic data designed to simulate real-world data quality issues)
Size: 12,575 transactions, 8 product categories, Jan 2022 – Jan 2025
Columns: Transaction ID, Customer ID, Category, Item, Price Per Unit, Quantity, Total Spent, Payment Method, Location, Transaction Date, Discount Applied
Tools used
Python (pandas) — data cleaning and initial exploratory analysis
SQL (SQLite) — cross-validated the same analysis using SQL queries
Power BI — interactive dashboard for the final presentation layer
Data cleaning

The raw dataset had 1,213 incomplete rows (9.6%) across Item, Price Per Unit, Quantity, and Total Spent, plus 4,199 missing Discount Applied values. Rather than dropping rows or guessing values, the missingness was diagnosed first — it turned out to follow exactly two non-random patterns:

Pattern	Rows	What was missing	How it was resolved
A	609	Item, Price	Verified that price is fixed per item and that (Category, Price) uniquely identifies an item. Solved Price = Total ÷ Quantity, then looked up Item — both recovered exactly.
B	604	Item, Quantity, Total	Item recovered exactly via the same (Category, Price) lookup. Quantity and Total couldn't be solved exactly (2 unknowns, 1 equation), so Quantity was estimated from that item's median and explicitly flagged (Quantity_Imputed = True) rather than presented as real data.
—	4,199	Discount Applied	Kept as an explicit "Unknown" category instead of guessing Yes/No.

Result: 0 rows dropped, 0 nulls remaining, every estimate transparently flagged for anyone using the data downstream.

See 01_data_cleaning.ipynb for the full step-by-step process.

Key findings

1. Category revenue is driven by price, not demand. Butchers leads revenue (₹2,17,158), but quantity sold is nearly identical across all 8 categories (8,400–8,850 units). The gap comes from price-per-unit — Milk Products underperforms specifically because it's priced lowest (₹21.41), not because it's less popular.

2. Revenue is flat over 3 years, with a reliable January spike. ₹5,38,317 (2022) → ₹5,13,824 (2023) → ₹5,52,413 (2024) — a dip and recovery, ending roughly 2.6% above where it started. January consistently outperforms the months around it in every complete year on record.

3. Online slightly outperforms in-store. ₹8,29,051 vs ₹8,02,344 (~3.3% higher), with more transactions and a marginally larger average order — both channels are healthy, neither is being neglected.

4. Cash still leads payment methods. ₹5,65,193, narrowly ahead of Digital Wallet and Credit Card — all three within 6% of each other, contradicting the assumption that digital payments dominate.

5. Discounting isn't moving average spend. ₹130.56 (discounted) vs ₹130.29 (not discounted) — a ₹0.27 difference, well within noise. The current discount strategy doesn't appear to be driving bigger baskets.

6. One product is a clear outlier. Item_3_EHE earned just ₹224 across 3 years — over 100x less than the top seller and far below the rest of the bottom 10. A concrete candidate for a pricing or listing review.

Validation

Every finding above was calculated independently in both pandas and SQL, and the results matched exactly in every case — a deliberate cross-check to catch mistakes before trusting the conclusions.

Dashboard

Built in Power BI: category revenue, monthly trend, payment method breakdown, and online vs in-store performance, all on one page.

View the dashboard (PDF)

Files in this repo
File	Description
retail_store_sales.csv	Raw, messy source data
01_data_cleaning.ipynb	Full cleaning process with explanations
cleaned_retail_sales.csv	Cleaned output, ready for analysis
dashboard_screenshot.pdf	Exported Power BI dashboard
retail_sales.pbix	Power BI source file
