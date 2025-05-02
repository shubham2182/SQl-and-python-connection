
# Alt-Mobility Dataset analysis 

## Overview

This project entails a comprehensive analysis of customer orders and payment data to derive actionable insights aimed at enhancing customer retention strategies. Utilizing SQL for data extraction and transformation, the analysis culminates in a visual dashboard that encapsulates key findings.

## Dataset

The analysis is based on two primary datasets:

- **customer_orders.csv**: Contains details of customer orders, including order dates, customer IDs, and order amounts.
- **payments.csv**: Encompasses payment information such as payment dates, customer IDs, and payment amounts.

## SQL Analysis

The SQL queries were executed using Jupyter Notebook, as documented in the [`SQL_queries.ipynb`](https://github.com/shubham2182/Alt-Mobility-Assignment/blob/main/SQL_queries.ipynb) file. Below is a summary of the key analyses performed:

### 1. Total Revenue Generated

```sql
SELECT SUM(payment_amount) AS total_revenue
FROM payments;
```

*This query calculates the total revenue generated from all payments.*

### 2. Monthly Revenue Trends

```sql
SELECT 
    DATE_TRUNC('month', payment_date) AS month,
    SUM(payment_amount) AS monthly_revenue
FROM payments
GROUP BY month
ORDER BY month;
```

*Analyzes revenue trends on a monthly basis to identify seasonal patterns.*

### 3. Top 5 Customers by Revenue

```sql
SELECT 
    customer_id,
    SUM(payment_amount) AS total_spent
FROM payments
GROUP BY customer_id
ORDER BY total_spent DESC
LIMIT 5;
```

*Identifies the top five customers contributing the most to the revenue.*

### 4. Customer Retention Rate

```sql
WITH first_orders AS (
    SELECT 
        customer_id,
        MIN(order_date) AS first_order_date
    FROM customer_orders
    GROUP BY customer_id
),
repeat_orders AS (
    SELECT 
        co.customer_id,
        COUNT(*) AS repeat_order_count
    FROM customer_orders co
    JOIN first_orders fo ON co.customer_id = fo.customer_id
    WHERE co.order_date > fo.first_order_date
    GROUP BY co.customer_id
)
SELECT 
    COUNT(*) AS total_customers,
    SUM(CASE WHEN repeat_order_count > 0 THEN 1 ELSE 0 END) AS retained_customers,
    ROUND(SUM(CASE WHEN repeat_order_count > 0 THEN 1 ELSE 0 END)::decimal / COUNT(*) * 100, 2) AS retention_rate_percentage
FROM repeat_orders;
```



