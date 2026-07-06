# Retail Sales Performance Dashboard

## Project Title

**Retail Sales Performance Dashboard**

## Project Overview

This Power BI project analyzes retail sales performance across revenue, profit, orders, regions, customer segments, products, categories, and time. The report is designed as a multi-page executive dashboard that helps stakeholders monitor business performance, compare year-over-year sales trends, identify high-performing products, and understand profitability across regions and categories.

The uploaded project includes a Power BI report file, four CSV data sources, dashboard screenshots, Power Query transformation screenshots, a data model screenshot, and a DAX measures screenshot.

> **Documentation note:** The project context is inferred from the uploaded Power BI file, screenshots, and CSV datasets. No separate business requirements document was uploaded.

## Business Problem

Retail decision-makers need a clear and interactive way to track sales performance across products, customer segments, regions, and time. Without a structured dashboard, it is difficult to quickly answer questions such as:

- Which regions generate the most revenue?
- Which products and categories are driving revenue and profit?
- How is revenue trending by month, quarter, and year?
- Which customer segment contributes the highest share of revenue?
- Where are there signs of declining year-over-year growth?

This dashboard addresses those questions by consolidating sales data into a clean Power BI semantic model and presenting key metrics through executive-ready visuals.

## Project Objectives

- Track core sales KPIs: total revenue, total profit, total orders, profit margin, and average order value.
- Compare revenue trends across months, quarters, and years.
- Analyze revenue and margin performance by region.
- Identify top-performing products by revenue.
- Compare customer segment and product category performance.
- Use DAX measures to support dynamic KPIs, rankings, YoY calculations, and conditional formatting.
- Build a recruiter-friendly portfolio project that demonstrates Power BI reporting, data modeling, Power Query, and DAX skills.

## Dashboard Preview

### Executive Overview

**Screenshot file:** `Executive Overview.png`

<img src="Executive%20Overview%282%29.png" alt="Executive Overview" width="900">

### Product & Customer Analysis

**Screenshot file:** `Product & Customer Analysis.png`

<img src="Product%20%26%20Customer%20Analysis%281%29.png" alt="Product & Customer Analysis" width="900">

### Sales Deep Dive

**Screenshot file:** `Sales Deep Dive.png`

<img src="Sales%20Deep%20Dive%281%29.png" alt="Sales Deep Dive" width="900">

### Data Model

**Screenshot file:** `Data model screenshot.png`

<img src="Data%20model%20screenshot%283%29.png" alt="Power BI Data Model" width="900">

## Dataset Information

The project uses four CSV datasets loaded into Power BI and modeled into one fact table and three dimension tables.

| Uploaded File | Power BI Table | Rows | Columns | Description |
|---|---:|---:|---:|---|
| `sales.csv` | `FactSales` | 5,000 | 8 source columns; 10 after transformations | Transaction-level sales fact table with order, date, customer, product, region, quantity, sales amount, and profit fields. |
| `customers.csv` | `DimCustomers` | 200 | 2 | Customer dimension containing customer IDs and customer segment. |
| `products.csv` | `DimProducts` | 50 | 4 | Product dimension containing product ID, product name, category, and subcategory. |
| `date.csv` | `DimDate` | 1,096 | 5 | Calendar dimension covering 2022-01-01 through 2024-12-31. |

### Key Data Fields

| Table | Fields |
|---|---|
| `FactSales` | `OrderID`, `Date`, `CustomerID`, `ProductID`, `Region`, `Quantity`, `SalesAmount`, `Profit`, `Profit Margin`, `Region Full` |
| `DimCustomers` | `CustomerID`, `Segment` |
| `DimProducts` | `ProductID`, `Category`, `SubCategory`, `ProductName` |
| `DimDate` | `Date`, `Year`, `Month`, `MonthName`, `Quarter` |

### Data Coverage

| Metric | Value |
|---|---:|
| Sales records | 5,000 |
| Distinct orders | 5,000 |
| Distinct customers | 200 |
| Distinct products | 50 |
| Customer segments | 2: Consumer, Corporate |
| Product categories | 3: Furniture, Office Supplies, Technology |
| Regions | 4: East, North, South, West |
| Date range | 2022-01-01 to 2024-12-31 |

## Dashboard Pages

| Page | Purpose | Main Visuals | Slicers / Filters |
|---|---|---|---|
| **Executive Overview** | Provides a high-level performance summary for revenue, profit, orders, margin, regional revenue, customer segment revenue, and monthly trend. | KPI cards, monthly revenue line chart, revenue by region bar chart, customer segment donut chart, key insights. | Year |
| **Product & Customer Analysis** | Analyzes product-level revenue and customer segment/category performance. | Top 10 products by revenue, product performance table, 100% stacked bar chart by segment and category, key insights. | Customer Segment, Year |
| **Sales Deep Dive** | Reviews quarterly revenue, YoY growth, regional margin, and average order value. | KPI cards, quarterly revenue by year, revenue and profit margin by region, quarterly revenue and YoY matrix, key insights. | Category, Year |

## KPIs

| KPI | Value / Description |
|---|---:|
| Total Revenue | **$13.72M** |
| Total Profit | **$2.44M** |
| Total Orders | **5,000** |
| Profit Margin | **17.76%** |
| Average Order Value | **$2,744.84** |
| Revenue YoY | Dynamic measure; dashboard prompts users to select a single year before showing the YoY value. |

## Data Cleaning

Power Query was used to prepare the data before modeling.

| Table | Cleaning / Preparation Steps |
|---|---|
| `DimCustomers` | Loaded `customers.csv`, transformed generic source columns to text, promoted headers, and assigned `CustomerID` and `Segment` as text fields. |
| `DimDate` | Loaded `date.csv`, promoted headers, converted `Date` to date type, `Year` and `Month` to whole number, and `MonthName` and `Quarter` to text. |
| `DimProducts` | Loaded `products.csv`, transformed generic source columns to text, promoted headers, and assigned product attributes as text fields. |
| `FactSales` | Loaded `sales.csv`, promoted headers, assigned correct data types, added `Profit Margin` as `Profit / SalesAmount`, rounded `SalesAmount`, `Profit`, and `Profit Margin`, and added `Region Full` as `Region & ", USA"`. |

> The Power Query screenshots show local source files named `customers.csv`, `date.csv`, `products.csv`, and `sales.csv`.

## Data Model

The data model follows a star schema with `FactSales` as the central fact table and three dimension tables.

| Relationship | Cardinality | Description |
|---|---|---|
| `DimCustomers[CustomerID]` → `FactSales[CustomerID]` | One-to-many | Connects each customer to sales records. |
| `DimProducts[ProductID]` → `FactSales[ProductID]` | One-to-many | Connects each product to sales records. |
| `DimDate[Date]` → `FactSales[Date]` | One-to-many | Enables time intelligence by year, quarter, month, and date. |

A separate `_TableMeasures` table stores DAX measures such as revenue, profit, orders, margin, rankings, YoY logic, and display helpers.

## DAX Measures

The following measures are visible in the uploaded DAX screenshot. Where the full formula is not visible in the screenshot, this documentation labels it as partial rather than guessing the missing logic.

| Measure Name | DAX Measure |
|---|---|
| Total Revenue | `SUM(FactSales[SalesAmount])` |
| Total Profit | `SUM(FactSales[Profit])` |
| Total Orders | `DISTINCTCOUNT(FactSales[OrderID])` |
| Profit Margin % | `DIVIDE([Total Profit], [Total Revenue], 0)` |
| Revenue YoY % | `VAR CurrentYear = [Total Revenue]` — **partial formula visible only** |
| Revenue YTD | `TOTALYTD([Total Revenue], DimDate[Date])` |
| Revenue LY | `CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(DimDate[Date]))` |
| Category Rank | `RANKX(ALL(DimProducts[Category]), [Total Revenue],, DESC)` |
| Region Rank | `RANKX(ALL(FactSales[Region]), [Total Profit],, DESC)` |
| Margin Color | `IF([Profit Margin %] > 0.17, "#27AE60", ... )` — **partial formula visible only** |
| Average Order Value | `DIVIDE([Total Revenue], [Total Orders], 0)` |
| Revenue YoY % Display | `IF(HASONEVALUE(DimDate[Year]), [Revenue YoY %], BLANK())` |
| Revenue YoY Message | `IF(HASONEVALUE(DimDate[Year]), FORMAT([Revenue YoY %], "0.00%"), "Select a year")` |

## Key Insights

- **East** generated the highest revenue at approximately **$3.56M**, followed by North at **$3.48M**.
- **Consumer** customers contributed approximately **51.72%** of total revenue, while Corporate contributed **48.28%**.
- **Furniture** was the highest revenue category at approximately **$5.12M**.
- **Technology** had the highest category-level profit margin at approximately **17.90%**.
- **Product 5** generated the highest product revenue at approximately **$347.68K**.
- **West** led regional profit margin performance at approximately **18.09%**.
- **Q2 2024** revenue declined by approximately **8.22% YoY**, making it a key period for business review.

## Tools & Technologies

- **Microsoft Power BI Desktop** — report creation, dashboard design, visual analytics.
- **Power Query** — data extraction, cleaning, type conversion, calculated columns, rounding, and preparation.
- **DAX** — KPI measures, rankings, time intelligence, dynamic YoY display, and conditional formatting logic.
- **CSV** — source data files.
- **Markdown** — GitHub-ready project documentation.

## How to Use

1. Clone or download this repository.
2. Open `Retail Sales Performance.pbix` in Power BI Desktop.
3. If Power BI prompts for source file paths, update the data source settings to point to the CSV files in the repository.
4. Refresh the dataset.
5. Review the report pages:
   - `Executive Overview`
   - `Product & Customer Analysis`
   - `Sales Deep Dive`
6. Use slicers such as Year, Customer Segment, and Category to filter and explore the dashboard.

## Future Improvements

- Replace local absolute Power Query source paths with relative or parameterized paths for easier repository reuse.
- Add a dedicated data dictionary file for all tables, columns, and measures.
- Include additional business dimensions such as discount, cost, sales channel, store, city, or customer demographics if available.
- Add drill-through pages for product-level or customer-level analysis.
- Add more advanced profitability analysis if cost, discount, or return data becomes available.
- Add automated refresh configuration after publishing to the Power BI Service.
- Include measure descriptions directly in the Power BI model for maintainability.