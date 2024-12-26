# supermarket-sales
Supermarket Sales Data Analysis SQL Project

## Project Overview
**Project Title** : Supermarket Sales Data Analysis

**Level**: Intermediate-Advanced

**Database**: supermarket_sales_db

**Tools Used**: MySQL

This project showcases my expertise in SQL for data analysis, where I explored, cleaned, and analyzed supermarket sales data to derive meaningful insights. The project covers Sales Analysis, Customer Behavior Trends, Anomaly Detection, Predictive Analysis, and Cohort Analysis to support business decision-making.

## Objectives

1. **Sales Performance**: Identify top-performing branches and product lines.
2. **Customer Behavior**: Analyze patterns by customer type, gender, and payment methods.
3. **Time-Based Trends**: Examine sales trends across different months, days, and hours.
4. **Anomaly Detection**: Flag unusual transactions using statistical measures.
5. **Predictive Analysis**: Forecast future sales based on historical trends.
6. **Cohort Analysis**: Understand customer retention patterns over time.

## My Approach
   ### Supermarket Sales Analysis
   
###  1. Sales Analysis
I started by analyzing overall sales performance:
- Calculated total sales and average sales per transaction.
- Identified top-performing product lines and branches based on revenue.

```sql
SELECT Branch, SUM(Total) AS Total_Sales
FROM supermarket_sales
GROUP BY Branch
ORDER BY Total_Sales DESC;
```

### 2.  Customer Behavior Analysis

In this project, I analyzed customer purchase behavior by:

- Analyzing sales by customer type (e.g., Member vs. Normal).
- Exploring preferred payment methods across branches.

```sql
SELECT Payment, SUM(Total) AS Total_Sales
FROM supermarket_sales
GROUP BY Payment
ORDER BY Total_Sales DESC;
```

### 3. Time-Based Trends Analysis

In this section, I explored sales trends across daily, monthly, and hourly timeframes to uncover valuable insights that can help optimize business operations.

1. **Peak Sales Hours**: 
   - I analyzed sales data by hour to determine peak sales times.
   - **Insight**: Peak sales occurred in the afternoon hours, particularly between **12 PM - 3 PM**, which can help in optimizing staffing and inventory during high-traffic periods.

2. **Monthly Sales Trends**: 
   - I analyzed monthly sales trends to identify seasonal performance and potential opportunities for promotional activities.
   
3. **Daily Sales Trends**: 
   - I examined sales data on a daily basis to understand patterns that could inform operational decisions.

```sql
SELECT HOUR(Time) AS Hour, SUM(Total) AS Total_Sales
FROM supermarket_sales
GROUP BY Hour
ORDER BY Total_Sales DESC;
````

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
