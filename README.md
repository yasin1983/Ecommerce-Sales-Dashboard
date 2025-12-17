# Ecommerce Sales Dashboard

## 1. Introduction
This project demonstrates end‑to‑end data analytics skills using SQL Server and Power BI. The dataset contains ecommerce sales transactions including product categories, order details, addresses, and timestamps. The goal is to showcase technical ability in data cleaning, SQL querying, and interactive dashboard design, while also highlighting business insights.

## 2. Data Source
File: Sales Data Analysis.csv

Rows: ~186,000 transactions

Columns: Order_ID, Product_Category, Product, Quantity_Ordered, Price_Each, Order_Date, Purchase_Address, Month, Sales, City, Hour, Time_of_Day

Imported into SQL Server for structured querying and validation.

## 3. SQL Setup
SQL Setup (via Import Wizard)
Instead of manually creating the table with CREATE TABLE, the dataset was imported using SQL Server Import and Export Wizard:

Source file: Sales Data Analysis.csv

Destination: [ECommerce].[dbo].[Sales Data Analysis]

Format: Delimited (CSV)

Text qualifier: " (to keep addresses intact)

Column names in first row: Enabled

Data types: Automatically inferred by Wizard

### Example Queries (after import)

## Example Request

```sql
-- Total Sales
SELECT SUM(Sales) AS TotalSales
FROM [ECommerce].[dbo].[Sales Data Analysis];

-- Sales by City
SELECT City, SUM(Sales) AS TotalSales
FROM [ECommerce].[dbo].[Sales Data Analysis]
GROUP BY City
ORDER BY TotalSales DESC;

-- Sales by Hour
SELECT Hour, SUM(Sales) AS TotalSales
FROM [ECommerce].[dbo].[Sales Data Analysis]
GROUP BY Hour
ORDER BY Hour;


```
## 4. Advanced SQL Queries (subqueries + CTEs)   

### Subquery Examples  

### 1. Highest‑selling city
``` sql
SELECT City, TotalSales
FROM (
    SELECT City, SUM(Sales) AS TotalSales
    FROM [ECommerce].[dbo].[Sales Data Analysis]
    GROUP BY City
) AS CitySales
WHERE TotalSales = (
    SELECT MAX(SumSales)
    FROM (
        SELECT SUM(Sales) AS SumSales
        FROM [ECommerce].[dbo].[Sales Data Analysis]
        GROUP BY City
    ) AS Sub
);

```

### 2. Products above average sales

``` sql
SELECT Product, SUM(Sales) AS TotalSales
FROM [ECommerce].[dbo].[Sales Data Analysis]
GROUP BY Product
HAVING SUM(Sales) > (
    SELECT AVG(SumSales)
    FROM (
        SELECT SUM(Sales) AS SumSales
        FROM [ECommerce].[dbo].[Sales Data Analysis]
        GROUP BY Product
    ) AS Sub
);


```
### CTE Examples
### 1. City sales totals

``` sql
WITH CitySales AS (
    SELECT City, SUM(Sales) AS TotalSales
    FROM [ECommerce].[dbo].[Sales Data Analysis]
    GROUP BY City
)
SELECT City, TotalSales
FROM CitySales
ORDER BY TotalSales DESC;


```
### 2. Top product per city

``` sql
WITH ProductSales AS (
    SELECT City, Product, SUM(Sales) AS TotalSales,
           RANK() OVER (PARTITION BY City ORDER BY SUM(Sales) DESC) AS RankByCity
    FROM [ECommerce].[dbo].[Sales Data Analysis]
    GROUP BY City, Product
)
SELECT City, Product, TotalSales
FROM ProductSales
WHERE RankByCity = 1;

```
### 3. Products above average sales (CTE version)

``` sql
WITH ProductTotals AS (
    SELECT Product, SUM(Sales) AS TotalSales
    FROM [ECommerce].[dbo].[Sales Data Analysis]
    GROUP BY Product
),
AverageSales AS (
    SELECT AVG(TotalSales) AS AvgSales
    FROM ProductTotals
)
SELECT Product, TotalSales
FROM ProductTotals, AverageSales
WHERE ProductTotals.TotalSales > AverageSales.AvgSales;

```
## 5. Power BI Dashboard
The dashboard includes:

- KPI Card → Total Sales

- Bar Chart → Sales by City

- Pie Chart → Sales by Product Category

- Line Chart → Sales by Hour

- Stacked Column Chart → Sales by Time of Day

- Slicers → Month, City, Product Category

## 6. Business Insights
- City Performance: Portland, New York City, and San Francisco lead in sales.

- Product Trends: Laptops drive revenue peaks; charging cables show consistent demand.

- Time Analysis: Afternoon and evening hours dominate sales activity.

- Seasonality: December shows spikes due to holiday shopping.

- Customer Behavior: Purchases cluster later in the day, aligning with leisure hours.

## 7. Business Value
- Optimize inventory management by city and product category.

- Target marketing campaigns during peak shopping hours.

- Align promotions with seasonal demand.

- Improve customer experience with region‑specific offers.