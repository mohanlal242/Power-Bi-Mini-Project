
Retail Sales Analysis: Excel Data Cleaning and Power BI Dashboard


Project Overview

This repository contains a mini data analytics project focused on cleaning, transforming, and analyzing raw retail sales data. The project bridges two powerful tools—Excel for data pre-processing and quality assurance, and Power BI for building an interactive, two-page dashboard that surfaces key business insights on regions, products, salespeople, and customer segments.

Repository Content

• Retail_sales_dataset.xlsx: The core Excel workbook containing three key sheets:
	• Raw RetailSalesData: The initial transactional data containing structural, encoding, and missing value errors.
	• Imputed Retail sales data: The cleaned, validated working copy that serves as the single source of truth loaded into Power BI.
	• Pivot Table: Excel native summaries tracking revenue metrics by salesperson and customer type.
• Retail_Sales_Dashboard.pbix: The Power BI Desktop file featuring a comprehensive, interactive data model and two dedicated report pages.

Data Source & Architecture

The dataset comprises 918 order records spanning from January 2022 to October 2026 across a multi-country, four-region distribution (APAC, EMEA, LATAM, NA).

Feature Details


Attribute	Data Type	Description
OrderID	String	Unique identifier (e.g., ORD-10001)
OrderDate	Date	Date transaction occurred
CustomerID	String	Unique identifier (e.g., CUST-1143)
ProductID	Categorical	Product code (P-series P101–P203; C-series C301–C304)
UnitsSold	Numeric	Total integer count per order (1 to 20)
PricePerUnit	Numeric	Unit selling price (45 to 100)
TotalRevenue	Numeric	Derived transaction value (UnitsSold × PricePerUnit)
Region	Categorical	Operational region (APAC, EMEA, LATAM, NA)
Country	Categorical	10 distinct countries involved
City	Categorical	12 distinct cities involved
SalesPerson	Categorical	Rep managing the transaction (9 unique reps)
CustomerType	Categorical	Segment categorization (New, Returning, VIP)


Data Pre-Processing & Cleaning Rules

Raw data contained heavy quality issues including roughly 27% missing values in the Region column, broken text encoding (SÃ£o Paulo), placeholder values (UNKNOWN), and trailing whitespace anomalies.
The following steps were implemented in Excel / Power Query to process the raw records:
1. Whitespace Trimming: Standardized text columns to unify duplicate string representations (e.g., resolving " APAC " to "APAC").
2. Geographic Imputation: Mapped Country directly to Region using relational rules (e.g., US/Canada → NA; Brazil/Argentina → LATAM) to fully recover all 251 missing regions. Cross-filled missing countries and cities based on corresponding localized entries.
3. Encoding & Text Correction: Corrected structural text rendering flaws (e.g., replacing SÃ£o Paulo with São Paulo) and replaced missing strings in SalesPerson, CustomerType, and ProductID fields with "Unknown" to protect transactional totals.
4. Deterministic Numeric Recovery: Rather than applying general mean values which skew integer counts, missing elements among TotalRevenue, UnitsSold, and PricePerUnit were mathematically derived using the underlying business rule: TotalRevenue = UnitsSold × PricePerUnit.
5. Deduplication & Formatting: Verified no fully duplicate records exist. Extracted text-based dates into native date formats while dropping unrecoverable placeholders.
Result after cleaning: 918 clean orders, total revenue of 677,843, 0 missing regions, and 0 revenue mismatches.

Data Modeling & DAX Measures

The dataset was loaded as a single flat table model inside Power BI. Key calculations are handled dynamically using custom DAX measures:
dax
// Core KPI Metrics
Total Revenue = SUM('Retail sales data'[Total Revenue])

Total Orders = COUNTROWS('Retail sales data')

Units Sold = SUM('Retail sales data'[Unite sold])

Avg Revenue per Order = DIVIDE([Total Revenue], [Total Orders])

// Distribution & Share Analysis
Revenue Share % = DIVIDE([Total Revenue], CALCULATE([Total Revenue], ALL('Retail sales data')))

// Advanced Time Intelligence (Requires Date Table relation)
YoY Growth % = 
DIVIDE(
    [Total Revenue] - CALCULATE([Total Revenue], SAMEPERIODLASTYEAR('Date'[Date])), 
    CALCULATE([Total Revenue], SAMEPERIODLASTYEAR('Date'[Date]))
)

Cumulative Revenue = 
CALCULATE(
    [Total Revenue], 
    FILTER(ALL('Date'[Date]), 'Date'[Date] <= MAX('Date'[Date]))
)
Use code with caution.
(Note: A custom Date table was utilized to properly leverage standard Power BI time-intelligence syntax like SAMEPERIODLASTYEAR and Date Hierarchy).

Power BI Dashboard Architecture

The report deploys a custom Navy visual palette (#1F2A44) spanning two pages with native cross-filtering, multi-level hierarchy drill-downs, and dynamic slicers:

Page 1: Sales Performance Analysis

• KPI Metric Cards: Displays high-level summaries for Total Revenue, Total Orders, Units Sold, Average Revenue per Order, Average Price per Unit, and Max Price per Unit.
• Revenue Trend Over Time: Line chart with drill-down layers across Year → Quarter → Month → Day to surface cyclical patterns.
• Revenue by Region: Donut chart tracking percentage-based regional share allocations.
• Revenue by Salesperson: Clustered column chart ranking individual representative performance.
• Revenue & Volume Breakdown: Line and clustered column combo chart comparing volume velocity (Units Sold) against overall revenue realization by country.
• Product Mix Matrix: Treemap organizing product contribution based on unique ProductID variables.

Page 2: Customer & Geographic Insights

• Geographic Spread: Filled map tracking structural location matrices down from Country to City levels.
• City Performance Ranking: Horizontal bar chart mapping performance of localized urban markets.
• Customer Mix Funnel: Funnel graphic outlining transaction counts generated across specific CustomerType buckets.
• Volume Distribution: Donut chart capturing proportional units sold per customer segment.
• Order-Level Detail Matrix: Granular tabular grid breaking out transactional elements across Order, Product, Customer, and Regional keys.

Key Executive Insights

• Regional Drivers: The APAC region leads global performance at 188,067 (28% share), closely followed by NA at 175,639 (26%). The USA stands out as the single highest revenue-generating country (104,585), while Buenos Aires represents the highest-performing specific city market.
• Product Stratification: Premium P-series products perform far better, averaging a value of 838 per order compared to the 583 average seen in the budget C-series line. Items P103 and P101 act as the primary revenue generators.
• Customer Lifetime Segments: New customers bring in the highest initial gross totals (230,640), but VIP segments prove their value with a significantly higher average order value (753) compared to Returning accounts (714).
• Seasonality Trends: Historical trends display a distinct Q4 performance peak across multiple consecutive fiscal years, indicating a recurring cyclical pattern.
