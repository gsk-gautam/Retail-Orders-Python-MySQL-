
# 📦 MySQL + Jupyter Integration Project

This project demonstrates how to:

- Connect a Pandas DataFrame to a MySQL database using SQLAlchemy.
- Load data from Jupyter Notebook into MySQL.
- Perform SQL analysis using queries in MySQL Workbench.

## 🔧 Setup

1. **Install dependencies**:
   ```bash
   pip install mysql-connector-python SQLAlchemy PyMySQL
   ```

2. **Create MySQL Table**:
   ```sql
   CREATE TABLE df_orders (
     order_id INT PRIMARY KEY,
     order_date DATE,
     ship_mode VARCHAR(20),
     segment VARCHAR(20),
     ...
     profit DECIMAL(7,2)
   );
   ```

3. **Upload Data from Jupyter**:
   ```python
   from sqlalchemy import create_engine
   engine = create_engine("mysql+pymysql://root:your_password@localhost:3306/project1")
   df.to_sql('df_orders', con=engine, if_exists='replace', index=False)
   ```

## 🔍 Sample SQL Queries

- **Top-selling month per category**  
- **Sub-category with highest profit growth (2023 vs 2022)**


---

👤 *Author: [Gautam Kumar](https://www.linkedin.com/in/gautam-kumar-a83bb3223/)*  
📧 gautamgsk009@gmail.com
