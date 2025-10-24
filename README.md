# Project 1 SQL - Yuan

## Project Overview

This project is to enrich my portfolio in  data analytics, this project is about sales transactions data

I have attached the raw data in excel

**1. First, create all the columns for the data**
-- Create TABLE
```sql
DROP TABLE IF EXISTS retail_sales; -- Drop table ini gunanya untuk hapus table
CREATE TABLE retail_sales
			(
				transactions_id INT PRIMARY KEY,
				sale_date DATE,
				sale_time TIME,
				customer_id INT,
				gender VARCHAR (15),
				age INT,
				category VARCHAR (15) ,
				quantity INT,
				price_per_unit FLOAT,
				cogs FLOAT,
				total_sale FLOAT
			);
```

**2. Import raw data to the table in the database**

**3. Make Sure of the imported data**
```sql
SELECT 
	COUNT (*) --> Hitung jumlah row, apakah yg di import benar segitu rows nya atau ada yg kurang?
FROM retail_sales
```
after that,
```sql
--> Cek satu2 table, cek tiap column untuk nilai NULL (yang kosong)
SELECT * FROM retail_sales
WHERE transactions_id IS NULL

SELECT * FROM retail_sales
WHERE sale_date IS NULL

SELECT * FROM retail_sales
WHERE sale_time IS NULL

--> ALTER//Daripada cek satu2 select where nya, mending satuin seperti ini:

SELECT * FROM retail_sales
WHERE 
	transactions_id IS NULL
	OR
	sale_date IS NULL
	OR
	sale_time IS NULL
	OR
	gender IS NULL
	OR
	age IS NULL
	OR
	category IS NULL
	OR
	quantity IS NULL
	OR
	price_per_unit IS NULL
	OR
	cogs IS NULL
	OR
	total_sale IS NULL;
```

This project is part of my portfolio











