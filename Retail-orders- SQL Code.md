# Extracting key insights on customer behavior and product trends from the Kaggle dataset joined with MySQL

### 1 Find top 10 highest revenue generating products
~~~
SELECT product_id, SUM(sale_price) AS sales
FROM df_orders
GROUP BY product_id
ORDER BY sales DESC
LIMIT 10;
~~~
![image](https://github.com/user-attachments/assets/fa9aafa7-e94c-4288-8baa-e1cbf225e88b)
--
### 2 Find top 5 highest selling products in each region
~~~
WITH cte AS (
    SELECT region, product_id, SUM(sale_price) AS sales,
           ROW_NUMBER() OVER (PARTITION BY region ORDER BY SUM(sale_price) DESC) AS rn
    FROM df_orders
    GROUP BY region, product_id
    ORDER BY region, sales DESC
)
SELECT region, product_id, sales
FROM cte
WHERE rn <= 5;
~~~

![image](https://github.com/user-attachments/assets/88f2a6fa-d109-4e94-81fa-a6c337cfa85e)
--

### 3 Find month over month growth comparison for 2022 and 2023 eg: Jan'22 vs Jan'23
~~~
WITH cte AS (
    SELECT YEAR(order_date) AS year, MONTH(order_date) AS month, SUM(sale_price) AS sales
    FROM df_orders
    GROUP BY 1, 2
)
SELECT month, 
       SUM(CASE WHEN year = 2022 THEN sales ELSE 0 END) AS sales_2022,
       SUM(CASE WHEN year = 2023 THEN sales ELSE 0 END) AS sales_2023
FROM cte
GROUP BY month
ORDER BY month;
~~~

![image](https://github.com/user-attachments/assets/4e5deda6-1d2a-47ff-963c-ecb7579ff250)
--

### 4 For each category which month as highest sales
~~~
WITH cte AS (
  SELECT 
    category, 
    DATE_FORMAT(order_date, '%Y%m') AS order_year_month,
    SUM(sale_price) AS sales,
    ROW_NUMBER() OVER (PARTITION BY category ORDER BY SUM(sale_price) DESC) AS rn
  FROM df_orders
  GROUP BY category, DATE_FORMAT(order_date, '%Y%m')
)

SELECT category, order_year_month, sales
FROM cte
WHERE rn = 1;
~~~

![image](https://github.com/user-attachments/assets/8c891294-54fe-466b-9944-81ea3ffc3023)
--

### 5 Which sub category had highest growth by profit in 2023 as compared to 2022
~~~
WITH profit_by_year AS (
  SELECT 
    sub_category,
    DATE_FORMAT(order_date, '%Y') AS order_year,
    SUM(profit) AS total_profit
  FROM df_orders
  WHERE DATE_FORMAT(order_date, '%Y') IN ('2022', '2023')
  GROUP BY sub_category, DATE_FORMAT(order_date, '%Y')
),
pivoted AS (
  SELECT 
    sub_category,
    MAX(CASE WHEN order_year = '2022' THEN total_profit ELSE 0 END) AS profit_2022,
    MAX(CASE WHEN order_year = '2023' THEN total_profit ELSE 0 END) AS profit_2023
  FROM profit_by_year
  GROUP BY sub_category
),
growth_calc AS (
  SELECT 
    sub_category,
    profit_2022,
    profit_2023,
    (profit_2023 - profit_2022) AS absolute_growth,
    ROUND(((profit_2023 - profit_2022) / NULLIF(profit_2022, 0)) * 100, 2) AS percentage_growth
  FROM pivoted
)

SELECT *
FROM growth_calc
ORDER BY percentage_growth DESC
LIMIT 1;
~~~

![image](https://github.com/user-attachments/assets/6eb6dfec-ef9d-46f2-8aeb-ba2a86a1e005)
--

