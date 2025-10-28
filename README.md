Overview

This project demonstrates the implementation of an ETL (Extract, Transform, Load) pipeline and a dimensional data mart to support analytical reporting.
The business process modeled is a retail sales scenario, where customer purchases (sales orders) are analyzed over time by customer and product dimensions.

The system integrates data from multiple sources — MySQL, MongoDB, and a CSV file — into a unified analytical schema stored in a MySQL data warehouse.

Process Summary
1. Design

The data mart follows a star schema design:

Fact Table: fact_sales – captures transactional sales data

Dimension Tables:

dim_customer – customer information (from MongoDB)

dim_product – product details (from CSV)

dim_date – date dimension for temporal analysis (from provided SQL script)

This structure enables analytical queries such as sales trends, product performance, and customer segmentation over time.

2. Extract

Data was extracted from multiple sources:

MySQL: Source transactional data (SalesOrderHeader, SalesOrderDetail)

MongoDB: Customer data imported from MySQL and re-extracted as a DataFrame

CSV File: Product data exported from MySQL and later reloaded

Functions used:

get_sql_dataframe() – retrieves SQL data as a Pandas DataFrame

get_mongo_dataframe() – retrieves MongoDB data as a DataFrame

3. Transform

Transformation steps included:

Selecting and renaming relevant columns

Joining dimension tables with the fact table

Generating surrogate keys and mapping them across tables

Converting timestamps to proper date formats for joining with the date dimension

4. Load

The transformed data was loaded into the destination MySQL data mart using the set_dataframe() function.

The .to_sql() method was used to create or append tables

Primary keys were added for each dimension and fact table

Destination tables:

dim_customer

dim_product

fact_sales

Code Description
Database Utility Functions

create_database() – creates the destination MySQL database

get_sql_dataframe() – runs SQL queries and returns results as a DataFrame

set_dataframe() – loads DataFrames into SQL tables

get_mongo_client() and get_mongo_dataframe() – connects to MongoDB and retrieves data

set_mongo_collections() – loads JSON data into MongoDB collections

ETL Workflow

Extract data from MySQL

Export some of it to CSV and MongoDB (to simulate multiple sources)

Retrieve and transform all datasets using Pandas

Merge dimensions and fact data into a star schema

Load the final schema into the MySQL data warehouse

Analytical Queries

Example queries to verify functionality:

-- Query 1: Total and average sales by customer and product
SELECT 
    dc.AccountNumber,
    dp.Name AS ProductName,
    SUM(fs.line_total) AS TotalSales,
    AVG(fs.unit_price) AS AvgUnitPrice
FROM fact_sales fs
JOIN dim_customer dc ON fs.customer_key = dc.CustomerID
JOIN dim_product dp ON fs.product_key = dp.ProductID
GROUP BY dc.AccountNumber, dp.Name
ORDER BY AccountNumber, TotalSales DESC
LIMIT 10;

-- Query 2: Sales by date, customer type, and product
SELECT 
    dd.full_date AS OrderDate,
    dc.CustomerType,
    dp.Name AS ProductName,
    SUM(fs.line_total) AS TotalSales,
    AVG(fs.unit_price) AS AvgUnitPrice,
    COUNT(fs.sales_order_id) AS NumOrders
FROM fact_sales fs
JOIN dim_customer dc ON fs.customer_key = dc.CustomerID
JOIN dim_product dp ON fs.product_key = dp.ProductID
JOIN dim_date dd ON fs.order_date_key = dd.date_key
GROUP BY dd.full_date, dc.CustomerType, dp.Name
ORDER BY dd.full_date ASC, TotalSales DESC
LIMIT 15;

Deployment Strategy

MySQL can be hosted locally or via cloud (AWS RDS, Azure Database for MySQL)

MongoDB can be hosted locally or in MongoDB Atlas

The Python ETL script runs in any environment with:

pandas, sqlalchemy, pymongo, certifi, pymysql, numpy

Database credentials (mysql_args, dstdb_args, mongodb_args) are defined externally for security

Data files (CSV/JSON) are stored in the /data directory for reproducibility

Repository contents:

etl_pipeline.py – main ETL script

/data – contains exported CSV/JSON files

README.md – documentation and usage instructions

Conclusion

This project demonstrates a complete ETL pipeline that integrates multi-source data into a dimensional data mart ready for analytical queries.

It fulfills all project requirements:

Includes a Date dimension

Two additional dimension tables

One fact table

Uses three data sources (SQL, NoSQL, and file system)

Includes aggregations and grouping in SQL queries for analysis
