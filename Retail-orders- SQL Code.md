# Extracting key insights on customer behavior and product trends from the Kaggle dataset joined with MySQL

### find top 10 highest revenue generating products
~~~
select product_id, sum(sale_price) as sales
from df_orders
group by product_id
order by sales desc
limit 10;
~~~
