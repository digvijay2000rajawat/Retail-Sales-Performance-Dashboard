# Retail Sales Performance Dashboard — Project Documentation

## Executive Summary

The **Retail Sales Performance Dashboard** is a Power BI analytics project built to monitor sales, profit, order volume, margin, product performance, customer segment contribution, regional performance, and YoY trends. The report uses a star-schema model with one fact table, three dimension tables, and a dedicated DAX measures table.

Across the uploaded sales dataset, the business generated **$13.72M** in total revenue, **$2.44M** in total profit, **5,000** distinct orders, and an overall **17.76%** profit margin. Revenue is distributed relatively evenly across the four regions, with **East** contributing the highest revenue and **West** leading on profit margin. At the customer level, **Consumer** customers generate a slightly larger share of revenue than Corporate customers. At the category level, **Furniture** leads revenue while **Technology** has the highest profit margin.

The dashboard is organized into three analytical pages:

1. **Executive Overview** — high-level business performance and summary insights.
2. **Product & Customer Analysis** — product revenue, product performance, and segment/category split.
3. **Sales Deep Dive** — quarterly trends, YoY performance, regional margin, and average order value.

## Business Problem

Retail stakeholders need to understand what is driving sales performance across time, geography, products, and customer segments. Transactional CSV data alone does not provide an immediate view of performance trends, profitability, or product-level drivers. This creates challenges in answering common business questions:

- Are revenue and profit growing over time?
- Which regions generate the most revenue?
- Which regions are more profitable?
- Which customer segment contributes more to revenue?
- Which products and categories should receive more commercial focus?
- Where are there signs of revenue decline that require business attention?

This Power BI project solves the problem by transforming raw CSV files into a structured analytical model and visualizing KPIs, trends, and business insights in an interactive dashboard.

## Project Objectives

- Build a clean Power BI semantic model from CSV source files.
- Create a star schema using sales, customer, product, and calendar data.
- Calculate core sales KPIs using DAX.
- Use time intelligence to support YTD, last-year revenue, and YoY analysis.
- Compare performance by year, quarter, month, region, customer segment, product, and category.
- Create an executive-ready report layout with slicers, KPI cards, charts, tables, and insights.
- Document the full project in a GitHub-ready format for portfolio and recruiter review.

## Dataset Description

### Source Files

| Uploaded File | Modeled Table | Row Count | Description |
|---|---:|---:|---|
| `sales.csv` | `FactSales` | 5,000 | Sales transaction table with order, date, customer, product, region, quantity, sales amount, and profit. |
| `customers.csv` | `DimCustomers` | 200 | Customer reference table with segment classification. |
| `products.csv` | `DimProducts` | 50 | Product reference table with category and subcategory fields. |
| `date.csv` | `DimDate` | 1,096 | Calendar table covering 2022, 2023, and 2024. |

### Dashboard and Supporting Files

| File | Purpose |
|---|---|
| `Retail Sales Performance.pbix` | Main Power BI report file. |
| `Executive Overview.png` | Screenshot of the Executive Overview dashboard page. |
| `Product & Customer Analysis.png` | Screenshot of the Product & Customer Analysis dashboard page. |
| `Sales Deep Dive.png` | Screenshot of the Sales Deep Dive dashboard page. |
| `Data model screenshot(3).png` | Screenshot of the Power BI model view. |
| `DimCustomer Applied Steps(3).png` | Screenshot of Power Query steps for `DimCustomers`. |
| `DimDate Applied Steps(3).png` | Screenshot of Power Query steps for `DimDate`. |
| `DimProducts Applied Steps(3).png` | Screenshot of Power Query steps for `DimProducts`. |
| `FactSales Applied Steps(3).png` | Screenshot of Power Query steps for `FactSales`. |
| `DAX Measures(3).png` | Screenshot listing DAX measure names and formulas. |

## Data Understanding

### Sales Fact Table: `FactSales`

| Column | Description | Data Type in Model |
|---|---|---|
| `OrderID` | Unique order identifier. | Text |
| `Date` | Sales transaction date. | Date |
| `CustomerID` | Customer key used to join to `DimCustomers`. | Text |
| `ProductID` | Product key used to join to `DimProducts`. | Text |
| `Region` | Sales region. | Text |
| `Quantity` | Quantity sold. | Whole number |
| `SalesAmount` | Revenue amount for the transaction. | Decimal number |
| `Profit` | Profit amount for the transaction. | Decimal number |
| `Profit Margin` | Calculated row-level profit margin. | Decimal number |
| `Region Full` | Concatenated region label in the format `Region, USA`. | Text |

### Dimension Tables

| Table | Columns | Notes |
|---|---|---|
| `DimCustomers` | `CustomerID`, `Segment` | Contains 200 customers split evenly between Consumer and Corporate segments. |
| `DimProducts` | `ProductID`, `Category`, `SubCategory`, `ProductName` | Contains 50 products across Furniture, Office Supplies, and Technology. |
| `DimDate` | `Date`, `Year`, `Month`, `MonthName`, `Quarter` | Calendar table from 2022-01-01 through 2024-12-31. |

### Data Quality Observations

| Check | Result |
|---|---:|
| Null values in uploaded CSVs | None detected. |
| Duplicate rows in uploaded CSVs | None detected. |
| Sales rows with missing customer keys | 0 |
| Sales rows with missing product keys | 0 |
| Sales rows with dates not present in `DimDate` | 0 |
| Distinct orders | 5,000 |

## Data Cleaning Process

The uploaded Power Query screenshots show a straightforward cleaning process focused on source loading, header promotion, data type conversion, calculated columns, and rounding.

### `DimCustomers`

```powerquery
Source = Csv.Document(File.Contents("...\customers.csv"), [Delimiter=",", Columns=2, Encoding=1252, QuoteStyle=QuoteStyle.None])
#"Changed Type" = Table.TransformColumnTypes(Source, {{"Column1", type text}, {"Column2", type text}})
#"Promoted Headers" = Table.PromoteHeaders(#"Changed Type", [PromoteAllScalars=true])
#"Changed Type1" = Table.TransformColumnTypes(#"Promoted Headers", {{"CustomerID", type text}, {"Segment", type text}})
```

### `DimDate`

```powerquery
Source = Csv.Document(File.Contents("...\date.csv"), [Delimiter=",", Columns=5, Encoding=1252, QuoteStyle=QuoteStyle.None])
#"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true])
#"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers", {{"Date", type date}, {"Year", Int64.Type}, {"Month", Int64.Type}, {"MonthName", type text}, {"Quarter", type text}})
```

### `DimProducts`

```powerquery
Source = Csv.Document(File.Contents("...\products.csv"), [Delimiter=",", Columns=4, Encoding=1252, QuoteStyle=QuoteStyle.None])
#"Changed Type" = Table.TransformColumnTypes(Source, {{"Column1", type text}, {"Column2", type text}, {"Column3", type text}, {"Column4", type text}})
#"Promoted Headers" = Table.PromoteHeaders(#"Changed Type", [PromoteAllScalars=true])
#"Changed Type1" = Table.TransformColumnTypes(#"Promoted Headers", {{"ProductID", type text}, {"Category", type text}, {"SubCategory", type text}, {"ProductName", type text}})
```

### `FactSales`

```powerquery
Source = Csv.Document(File.Contents("...\sales.csv"), [Delimiter=",", Columns=8, Encoding=1252, QuoteStyle=QuoteStyle.None])
#"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true])
#"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers", {{"OrderID", type text}, {"Date", type date}, {"CustomerID", type text}, {"ProductID", type text}, {"Region", type text}, {"Quantity", Int64.Type}, {"SalesAmount", type number}, {"Profit", type number}})
#"Added Custom" = Table.AddColumn(#"Changed Type", "Profit Margin", each [Profit] / [SalesAmount])
#"Changed Type1" = Table.TransformColumnTypes(#"Added Custom", {{"Profit Margin", type number}})
#"Rounded Off" = Table.TransformColumns(#"Changed Type1", {{"SalesAmount", each Number.Round(_, 2), type number}, {"Profit", each Number.Round(_, 2), type number}, {"Profit Margin", each Number.Round(_, 2), type number}})
#"Added Custom1" = Table.AddColumn(#"Rounded Off", "Region Full", each [Region] & ", USA")
```

## Data Transformation

Key transformations identified from the uploaded Power Query screenshots:

| Transformation | Purpose |
|---|---|
| Header promotion | Converts raw CSV first-row values into usable column headers. |
| Type conversion | Ensures fields such as date, text, integers, and decimal numbers behave correctly in visuals and DAX. |
| `Profit Margin` column | Adds row-level profitability context using `Profit / SalesAmount`. |
| Rounding | Rounds `SalesAmount`, `Profit`, and `Profit Margin` for cleaner reporting. |
| `Region Full` column | Creates a more descriptive region label by appending `, USA`. |

## Data Modeling

The model uses a star-schema structure.

```text
DimCustomers     DimProducts
     |                |
     |                |
     +---- FactSales--+
              |
              |
           DimDate
```

### Relationships

| From Table | From Column | To Table | To Column | Cardinality |
|---|---|---|---|---|
| `DimCustomers` | `CustomerID` | `FactSales` | `CustomerID` | One-to-many |
| `DimProducts` | `ProductID` | `FactSales` | `ProductID` | One-to-many |
| `DimDate` | `Date` | `FactSales` | `Date` | One-to-many |

### Measure Table

The `_TableMeasures` table contains report measures and keeps calculations organized separately from raw fact and dimension fields. Measures visible in the uploaded model screenshot include:

- `Average Order Value`
- `Category Rank`
- `Margin Color`
- `Profit Margin %`
- `Region Rank`
- `Revenue LY`
- `Revenue YoY %`
- `Revenue YoY % Display`
- `Revenue YoY Message`
- `Revenue YTD`
- `Total Orders`
- `Total Profit`
- `Total Revenue`

## DAX Measures

> The following table is based on the uploaded `DAX Measures(3).png` screenshot. Some formulas are only partially visible in the screenshot; those are clearly marked as partial.

| Measure | Formula | Notes |
|---|---|---|
| `Total Revenue` | `SUM(FactSales[SalesAmount])` | Calculates total sales revenue. |
| `Total Profit` | `SUM(FactSales[Profit])` | Calculates total profit. |
| `Total Orders` | `DISTINCTCOUNT(FactSales[OrderID])` | Counts unique orders. |
| `Profit Margin %` | `DIVIDE([Total Profit], [Total Revenue], 0)` | Safely divides profit by revenue. |
| `Revenue YoY %` | `VAR CurrentYear = [Total Revenue]` | Partial formula visible only. Full calculation logic is not fully shown in the uploaded screenshot. |
| `Revenue YTD` | `TOTALYTD([Total Revenue], DimDate[Date])` | Calculates year-to-date revenue. |
| `Revenue LY` | `CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(DimDate[Date]))` | Calculates same-period-last-year revenue. |
| `Category Rank` | `RANKX(ALL(DimProducts[Category]), [Total Revenue],, DESC)` | Ranks categories by revenue. |
| `Region Rank` | `RANKX(ALL(FactSales[Region]), [Total Profit],, DESC)` | Ranks regions by profit. |
| `Margin Color` | `IF([Profit Margin %] > 0.17, "#27AE60", ... )` | Partial formula visible only; used for conditional formatting. |
| `Average Order Value` | `DIVIDE([Total Revenue], [Total Orders], 0)` | Calculates average revenue per order. |
| `Revenue YoY % Display` | `IF(HASONEVALUE(DimDate[Year]), [Revenue YoY %], BLANK())` | Displays YoY only when one year is selected. |
| `Revenue YoY Message` | `IF(HASONEVALUE(DimDate[Year]), FORMAT([Revenue YoY %], "0.00%"), "Select a year")` | Shows a user-friendly message when year context is not singular. |

## Dashboard Design

### 1. Executive Overview

**Purpose:** Provide a quick executive summary of overall performance.

**Visuals and components:**

- KPI cards for Total Revenue, Total Profit, Total Orders, and Profit Margin.
- Monthly Revenue Trend line chart comparing total revenue against last-year revenue.
- Revenue by Region horizontal bar chart.
- Revenue by Customer Segment donut chart.
- Year slicer.
- Key Insights text box.

**Key values displayed:**

| KPI | Value |
|---|---:|
| Total Revenue | $13.72M |
| Total Profit | $2.44M |
| Total Orders | 5,000 |
| Profit Margin | 17.76% |

### 2. Product & Customer Analysis

**Purpose:** Identify product-level drivers and compare customer segment/category revenue composition.

**Visuals and components:**

- Top 10 Products by Revenue bar chart.
- Product Performance Details table with revenue, profit, margin, and orders.
- Revenue Split by Segment and Category 100% stacked bar chart.
- Customer Segment slicer.
- Year slicer.
- Key Insights text box.

**Top products by revenue:**

| Rank | Product | Category | Revenue | Profit | Orders | Profit Margin |
|---:|---|---|---:|---:|---:|---:|
| 1 | Product 5 | Technology | $347,683.18 | $59,481.30 | 120 | 17.11% |
| 2 | Product 26 | Technology | $345,741.38 | $64,626.76 | 116 | 18.69% |
| 3 | Product 29 | Office Supplies | $333,764.90 | $60,042.66 | 114 | 17.99% |
| 4 | Product 19 | Technology | $330,064.49 | $61,606.46 | 116 | 18.66% |
| 5 | Product 10 | Office Supplies | $322,019.90 | $56,327.38 | 108 | 17.49% |

### 3. Sales Deep Dive

**Purpose:** Analyze quarterly revenue trends, YoY growth, regional profitability, and average order value.

**Visuals and components:**

- KPI cards for Total Revenue, Revenue YoY message, Profit Margin, and Average Order Value.
- Quarterly Revenue by Year clustered column chart.
- Revenue & Profit Margin by Region combo chart.
- Quarterly Revenue and YoY Growth matrix.
- Category slicer.
- Year slicer.
- Key Insights text box.

**Quarterly revenue and YoY performance:**

| Year | Q1 Revenue | Q1 YoY | Q2 Revenue | Q2 YoY | Q3 Revenue | Q3 YoY | Q4 Revenue | Q4 YoY |
|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 2022 | $1,075,692.37 | N/A | $1,140,941.18 | N/A | $1,069,920.88 | N/A | $1,102,448.39 | N/A |
| 2023 | $1,185,764.35 | 10.23% | $1,179,228.93 | 3.36% | $1,160,248.59 | 8.44% | $1,125,857.57 | 2.12% |
| 2024 | $1,202,960.28 | 1.45% | $1,082,344.71 | -8.22% | $1,197,258.87 | 3.19% | $1,201,543.20 | 6.72% |

## Business Insights

### Overall Performance

- The dashboard reports **$13.72M** total revenue and **$2.44M** total profit.
- Overall profit margin is **17.76%**.
- Average order value is **$2,744.84** across **5,000** orders.

### Regional Performance

| Region | Revenue | Profit | Orders | Profit Margin |
|---|---:|---:|---:|---:|
| East | $3,562,834.33 | $627,739.26 | 1,282 | 17.62% |
| North | $3,477,387.81 | $615,615.20 | 1,262 | 17.70% |
| West | $3,350,895.77 | $606,337.23 | 1,215 | 18.09% |
| South | $3,333,091.41 | $588,259.44 | 1,241 | 17.65% |

**Insight:** East leads revenue, but West leads margin. This suggests that the highest-revenue region is not necessarily the most profitable by percentage margin.

### Customer Segment Performance

| Segment | Revenue | Profit | Orders | Revenue Share |
|---|---:|---:|---:|---:|
| Consumer | $7,098,152.08 | $1,257,534.15 | 2,579 | 51.72% |
| Corporate | $6,626,057.24 | $1,180,416.98 | 2,421 | 48.28% |

**Insight:** Consumer contributes slightly more revenue than Corporate, but the difference is not extreme. Both segments remain commercially important.

### Category Performance

| Category | Revenue | Profit | Orders | Profit Margin | Revenue Share |
|---|---:|---:|---:|---:|---:|
| Furniture | $5,117,917.91 | $905,489.17 | 1,888 | 17.69% | 37.29% |
| Office Supplies | $4,555,675.94 | $807,470.40 | 1,664 | 17.72% | 33.19% |
| Technology | $4,050,615.47 | $724,991.56 | 1,448 | 17.90% | 29.51% |

**Insight:** Furniture is the highest revenue category, while Technology has the strongest margin.

### Trend Performance

| Year | Revenue | Profit | Orders | Profit Margin | Revenue YoY |
|---:|---:|---:|---:|---:|---:|
| 2022 | $4,389,002.82 | $773,106.46 | 1,613 | 17.61% | N/A |
| 2023 | $4,651,099.44 | $835,264.93 | 1,686 | 17.96% | 5.97% |
| 2024 | $4,684,107.06 | $829,579.74 | 1,701 | 17.71% | 0.71% |

**Insight:** Revenue increased from 2022 to 2023 by approximately 5.97%, then growth slowed substantially to approximately 0.71% in 2024.

### Product Performance

- Product 5 generated the highest revenue at **$347.68K**.
- Four of the top five products by revenue belong to Technology or Office Supplies.
- Product 26, Product 19, and Product 21 combine high revenue with comparatively strong profit margins among the top products.

## Recommendations

- **Investigate Q2 2024 decline:** Q2 2024 revenue declined by approximately **8.22% YoY**. Review campaigns, seasonality, inventory, pricing, and customer demand factors for this quarter.
- **Protect East region revenue:** East is the highest revenue region, so maintaining customer acquisition, retention, and availability in this region should remain a priority.
- **Study West margin drivers:** West has the highest regional profit margin. Review whether pricing, product mix, or operational efficiency in the West can be replicated in other regions.
- **Balance category strategy:** Furniture leads revenue, while Technology leads profit margin. Growth strategies should consider both revenue volume and margin quality.
- **Leverage top products:** Products 5, 26, 29, 19, and 10 should be monitored closely because they are key revenue contributors.
- **Expand customer segmentation:** Consumer slightly outperforms Corporate in revenue share. Additional customer attributes would help identify more specific growth opportunities within each segment.

## Limitations

- Product names are generic, such as `Product 5`, which limits product-level interpretation outside the dashboard.
- The customer dataset includes only `CustomerID` and `Segment`, so detailed customer demographic or behavioral analysis is not possible.
- The dataset does not include fields such as discount, unit cost, sales channel, store, salesperson, return status, or shipping information.
- The uploaded DAX screenshot does not fully show every formula, specifically the full `Revenue YoY %` and `Margin Color` logic.
- Power Query screenshots show local absolute file paths, which may need to be updated when the report is opened on another machine.
- Recommendations are based on the available uploaded files only and should be validated with additional business context before operational decisions are made.

## Future Enhancements

- Add a complete data dictionary for all tables, columns, measures, and transformations.
- Add real product names, brand attributes, pricing fields, and cost fields if available.
- Add drill-through pages for product, region, and customer segment analysis.
- Add decomposition tree or key influencer visuals to identify margin drivers.
- Add rolling 12-month revenue and profit metrics.
- Add dynamic titles that reflect slicer selections.
- Add bookmarks for executive, regional, and product-focused views.
- Parameterize Power Query file paths to support easier refresh across machines.
- Publish the report to Power BI Service and configure scheduled refresh.

## Skills Demonstrated

- Data profiling and validation.
- CSV data ingestion.
- Power Query transformations.
- Star schema data modeling.
- One-to-many relationship design.
- DAX KPI measures.
- DAX time intelligence.
- Revenue, profit, order, margin, and YoY analysis.
- Dashboard layout and visual design.
- Business insight generation.
- Technical documentation and GitHub-ready project packaging.