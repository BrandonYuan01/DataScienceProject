# DataScienceProject

## Overview

This project demonstrates the implementation of an **ETL (Extract, Transform, Load)** pipeline and a **dimensional data mart** to support post hoc diagnostic analysis. 
The business process modeled is a **retail sales** scenario, where customer purchases (sales orders) are analyzed over time by customer and product dimensions.

The data mart integrates data from multiple sources, **MySQL**, **MongoDB**, and a **CSV file**, into a data warehouse.

---

## Process Summary

### 1. Design

The data mart has the following design:

- **Fact Table:** `fact_sales` – captures transactional sales data  
- **Dimension Tables:**
  - `dim_customer` – customer information (from MongoDB)  
  - `dim_product` – product details (from CSV)  
  - `dim_date` – date dimension for temporal analysis (from provided SQL script)

This design enables analytical queries like sales trends and product performance over time.

---

### 2. Extract

Data was extracted from multiple sources:

- **MySQL:** `SalesOrderHeader`, `SalesOrderDetail` tables were extracted from the AdventureWorks MySQL database
- **MongoDB:** Customer data exported from MySQL and then imported back as a DataFrame  
- **CSV File:** Product data exported from MySQL and then imported back as a Data Frame

---

### 3. Transform

Transformation steps included:

- Selecting and renaming relevant columns  
- Joining the 3 dimension tables with the fact table
- Merging on surrogate keys

---

### 4. Load

The transformed data was loaded into the destination MySQL data mart using the `set_dataframe()` function.

- The `.to_sql()` method was used to create or append tables  
- Primary keys were added for each dimension and fact table  

Destination tables:

- `dim_customer`  
- `dim_product`  
- `fact_sales`  

---

## Code Description

### ETL Workflow

1. Extract data from MySQL  
2. Export some of it to CSV and MongoDB (to simulate multiple sources)  
3. Retrieve and transform all datasets using Pandas  
4. Merge dimensions and fact data into a star schema  
5. Load the final schema into the MySQL data warehouse  
