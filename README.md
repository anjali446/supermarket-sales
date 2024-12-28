# Supermarket-sales
Supermarket Sales Data Analysis SQL Project

## Project Overview
- **Project Title** : Supermarket Sales Data Analysis
- **Level**: Intermediate-Advanced
- **Database**: supermarket_sales_db
- **Tools Used**: MySQL

This project showcases my expertise in SQL for data analysis, where I explored, cleaned, and analyzed supermarket sales data to derive meaningful insights. The project covers Sales Analysis, Customer Behavior Trends, Anomaly Detection, Predictive Analysis, and Cohort Analysis to support business decision-making.

## Objectives
1. **Sales Performance**: Identify top-performing branches and product lines.
2. **Customer Behavior**: Analyze patterns by customer type, gender, and payment methods.
3. **Time-Based Trends**: Examine sales trends across different months, days, and hours.
4. **Anomaly Detection**: Flag unusual transactions using statistical measures.
5. **Predictive Analysis**: Forecast future sales based on historical trends.
6. **Cohort Analysis**: Understand customer retention patterns over time.

## Project Structure
### Database Setup
- **Database Creation**: The database supermarket_sales_db stores all relevant data.
- **Table Structure**: A table named supermarket_sales contains details such as invoice ID, branch, city, customer type, gender, Unit price ,Quantity ,Tax 5%, Total, Date, Time,Payment ,cogs ,gross margin percentage ,gross income , Rating

```sql
CREATE DATABASE supermarket_sales_db;

CREATE TABLE supermarket_sales (
    Invoice_ID VARCHAR(10) PRIMARY KEY,
    Branch VARCHAR(3),
    City VARCHAR(50),
    Customer_type VARCHAR(10),
    Gender VARCHAR(10),
    Product_line VARCHAR(50),
    Unit_price DECIMAL(10, 2),
    Quantity INT,
    Tax_5 DECIMAL(10, 2),
    Total DECIMAL(10, 2),
    Date DATE,
    Time TIME,
    Payment VARCHAR(15),
    cogs DECIMAL(10, 2),
    gross_margin_percentage DECIMAL(5, 2),
    gross_income DECIMAL(10, 2),
    Rating DECIMAL(3, 1)
);
```
### Data Cleaning
**Checked for Missing Values**:
```sql
SELECT * 
FROM supermarket_sales
WHERE Invoice_ID IS NULL
   OR Branch IS NULL
   OR City IS NULL
   OR Customer_type IS NULL
   OR Gender IS NULL
   OR Product_line IS NULL
   OR Unit_price IS NULL
   OR Quantity IS NULL
   OR Tax_5 IS NULL
   OR Total IS NULL
   OR Date IS NULL
   OR Time IS NULL
   OR Payment IS NULL
   OR cogs IS NULL
   OR gross_margin_percentage IS NULL
   OR gross_income IS NULL
   OR Rating IS NULL;
```


### Data Analysis
 ### 1. Sales Analysis
I started by analyzing overall sales performance:
- Calculated total sales and average sales per transaction.
```sql
SELECT 
    SUM(Total) AS Total_Sales,
    AVG(Total) AS Average_Sales_Per_Transaction
FROM supermarket_sales;
```
- Identified top performing product lines based on Revenue.
```sql
SELECT Product_line, SUM(Total) AS Total_Sales
FROM supermarket_sales
GROUP BY Product_line
ORDER BY Total_Sales DESC
LIMIT 5;
```
- Identified top-Performing Branches Based on Revenue
```sql
SELECT Branch, SUM(Total) AS Total_Sales
FROM supermarket_sales
GROUP BY Branch
ORDER BY Total_Sales DESC;
```

### 2.  Customer Behavior Analysis

In this project, I analyzed customer purchase behavior by:
- Analyzing sales by customer type (e.g., Member vs. Normal).
```sql
SELECT Customer_type, SUM(Total) AS Total_Sales
FROM supermarket_sales
GROUP BY Customer_type
ORDER BY Total_Sales DESC;
```
- Exploring preferred payment methods across branches.

```sql
SELECT Payment, SUM(Total) AS Total_Sales
FROM supermarket_sales
GROUP BY Payment
ORDER BY Total_Sales DESC;
```

### 3. Time-Based Trends Analysis

In this section, I explored sales trends across daily, monthly, and hourly timeframes to uncover valuable insights that can help optimize business operations.

- **Peak Sales Hours**: 
Analyzed sales data by hour to determine peak sales times.

```sql
SELECT HOUR(Time) AS Hour, SUM(Total) AS Total_Sales
FROM supermarket_sales
GROUP BY Hour
ORDER BY Total_Sales DESC;
````
- **Monthly Sales Trends**:
Analyzed monthly sales trends to identify seasonal performance and potential opportunities for promotional activities.
```sql
SELECT DATE_FORMAT(Date, '%Y-%m') AS Month, SUM(Total) AS Total_Sales
FROM supermarket_sales
GROUP BY Month
ORDER BY Month;
```
### 4. Anomaly Detection in Sales Data

In this section, I applied statistical techniques to detect unusually high or low sales transactions, using mean and standard deviation. This helps to identify potential errors, fraud, or outliers in the data that could require further investigation or corrective action.

1. **Unusually High Sales**: By identifying transactions significantly higher than the average sales, we can investigate reasons behind spikes, such as promotions or special events.
2. **Unusually Low Sales**: Similarly, detecting unusually low transactions helps in identifying underperforming periods or possible data quality issues.

```sql
WITH SalesStats AS (
    SELECT 
        AVG(Total) AS Avg_Sales,
        STDDEV(Total) AS StdDev_Sales
    FROM supermarket_sales
)
SELECT Invoice_ID, Branch, Total
FROM supermarket_sales, SalesStats
WHERE Total > (Avg_Sales + 2 * StdDev_Sales) 
   OR Total < (Avg_Sales - 2 * StdDev_Sales);
```

### 5. Predictive Analysis

In this section, I forecasted future sales based on historical monthly sales trends. The prediction is made by calculating the average monthly sales, which can be used as a baseline target for future sales.

```sql
WITH MonthlySales AS (
    SELECT 
        DATE_FORMAT(Date, '%Y-%m') AS Month,
        SUM(Total) AS Monthly_Sales
    FROM supermarket_sales
    GROUP BY Month
)
SELECT 'Next_Month' AS Forecast_Period,
       AVG(Monthly_Sales) AS Predicted_Sales
FROM MonthlySales;
```

### 6. Cohort Analysis

I analyzed customer retention trends by grouping customers based on their first purchase month and tracking their purchasing behavior across subsequent months.

```sql
WITH FirstPurchase AS (
    SELECT 
        Customer_type,
        MIN(Date) AS First_Purchase_Date
    FROM supermarket_sales
    GROUP BY Customer_type
)
SELECT 
 FP.Customer_type,
    DATE_FORMAT(FP.First_Purchase_Date, '%Y-%m') AS Cohort_Month,
    DATE_FORMAT(s.Date, '%Y-%m') AS Purchase_Month,
    COUNT(s.Invoice_ID) AS Purchase_Count
FROM supermarket_sales s
JOIN FirstPurchase FP ON s.Customer_type = FP.Customer_type
GROUP BY FP.Customer_type, Cohort_Month, Purchase_Month;
```
### Insights

- **Top Branch**: Branch C had the highest total sales across all branches.
- **Customer Behavior**: Members contributed significantly more to sales than non-members.
- **Peak Hours**: Most sales occurred between 12 PM and 3 PM, indicating lunchtime shopping trends.
- **Anomalies**: Detected extreme transactions likely due to promotions or bulk purchases.
- **Sales Forecasting**: Predicted consistent sales growth based on historical trends.
- **Customer Retention**: Cohort analysis reveals consistent retention among members compared to non-members.

### Conclusion
The analysis provides actionable insights into branch performance, customer behavior, and time-based sales trends. It highlights peak activity periods and identifies potential outliers, enabling targeted strategies for promotions, inventory management, and customer retention.





