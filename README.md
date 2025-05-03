# 🛍️ Retail Sales Analysis SQL Project

## 📘 Overview

**Project Title**: Retail Sales Analysis  
**Database**: `p1_retail_db`  

This project showcases essential SQL skills applied to a retail dataset — from database creation and data cleaning to generating business insights. It’s designed for beginners to practice and build strong SQL foundations using real-world retail scenarios.

---

## 🎯 Objectives

1. **Database Setup** – Build and populate a structured retail sales database.
2. **Data Cleaning** – Detect and remove incomplete or missing data entries.
3. **Exploratory Data Analysis (EDA)** – Understand the structure and nature of the dataset.
4. **Business Insights** – Use SQL queries to extract actionable business insights.

---

## 🗂️ Project Structure

### 1. 🏗️ Database Setup

Create the database and main table:

```sql
CREATE DATABASE p1_retail_db;

CREATE TABLE retail_sales (
    transactions_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,
    cogs FLOAT,
    total_sale FLOAT
);
```

---

### 2. 🧹 Data Exploration & Cleaning

Count records and explore unique values:

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;
```

Identify and delete records with missing data:

```sql
SELECT * FROM retail_sales
WHERE sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
      gender IS NULL OR age IS NULL OR category IS NULL OR 
      quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

DELETE FROM retail_sales
WHERE sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
      gender IS NULL OR age IS NULL OR category IS NULL OR 
      quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

---

### 3. 📊 Business Analysis (SQL Queries)

**Sales on a specific day:**

```sql
SELECT * FROM retail_sales WHERE sale_date = '2022-11-05';
```

**Clothing sales (≥ 4 items) during November 2022:**

```sql
SELECT * FROM retail_sales
WHERE category = 'Clothing'
  AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
  AND quantity >= 4;
```

**Total sales per category:**

```sql
SELECT category, SUM(total_sale) AS net_sale, COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
```

**Average age of customers in 'Beauty' category:**

```sql
SELECT ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

**Transactions with total sales > 1000:**

```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000;
```

**Transactions by gender and category:**

```sql
SELECT category, gender, COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

**Best-performing month each year by average sales:**

```sql
SELECT year, month, avg_sale
FROM (
    SELECT 
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER (PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) AS rank
    FROM retail_sales
    GROUP BY year, month
) t
WHERE rank = 1;
```

**Top 5 customers by total sales:**

```sql
SELECT customer_id, SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

**Unique customers per category:**

```sql
SELECT category, COUNT(DISTINCT customer_id) AS cnt_unique_cs
FROM retail_sales
GROUP BY category;
```

**Order count by shift (Morning <12, Afternoon 12–17, Evening >17):**

```sql
WITH hourly_sale AS (
    SELECT *,
           CASE
               WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
               WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
               ELSE 'Evening'
           END AS shift
    FROM retail_sales
)
SELECT shift, COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;
```

---

## 📌 Key Insights

- **Customer Demographics**: Diverse age groups, top categories include Clothing and Beauty.
- **High-Value Sales**: Transactions over 1000 highlight premium segments.
- **Trends**: Monthly averages help identify seasonal spikes.
- **Top Customers**: Highest-spending customers and popular categories are easily identified.

---

## 📊 Reports Summary

- **Sales Summary**: Total sales, order count, category performance.
- **Trends**: Sales by month and customer activity by time of day.
- **Customer Insights**: Top buyers, unique customer breakdown.

---

## ✅ Getting Started

1. **Clone Repo**: Pull this project from GitHub.
2. **Set Up Database**: Run `database_setup.sql` to create and populate tables.
3. **Analyze**: Use `analysis_queries.sql` to run insights.
4. **Explore Further**: Modify queries to answer your own business questions.
