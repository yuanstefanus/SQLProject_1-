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

**4. OPTIONAL// Delete/clean the data that contains NULL**
```sql
DELETE FROM retail_sales --Ini tergantung prosedur, bisa jadi disuruh DELETE data yg punya null
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

**5. Reassure, check the rows**
```sql
SELECT 
	COUNT (*)
FROM retail_sales --Cek jumlah rows to make sure data yg tadi beneran kehapus or not
Cek lagi data yg punya value NULL pake syntax yg di atas tadi 
```
**6. Then, all set !**

### Next, sorting data: 

Contoh soal kecil-kecilan
```sql
-- cek jumlah customer ?
SELECT COUNT (DISTINCT customer_id) as customers FROM retail_sales -- DISCTINT untuk menghilangkan redundan/data double

-- cek jumlah kategori ?
SELECT COUNT (DISTINCT category) as total_category FROM retail_sales -- result nya angka

-- How many categories (nama nya/ apa apa saja)
SELECT DISTINCT category FROM retail_sales
```

Lets get to the real case !
```sql
--Soal-soal data analysis real-life:
-- 1. Write a SQL query to retrieve all columns for sales made on '2022-11-05: 
-- 2. Write a SQL query to retrieve all transactions where category is 'Clothing' and the quantity sold is more than 10 in the month of Nov-2022
-- 3. Write a SQL query to calculate the total sales (total_sale) for each category.:
-- 4. Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.:
-- 5. Write a SQL query to find all transactions where the total_sale is greater than 1000.:
-- 6. Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.:
-- 7. Write a SQL query to calculate the average sale for each month. Find out best selling month in each year:
-- 8. Write a SQL query to find the top 5 customers based on the highest total sales:
-- 9. Write a SQL query to find the number of unique customers who purchased items from each category.:
-- 10. Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17):
```

### Let's get to work !
**1. Write a SQL query to retrieve all columns for sales made on '2022-11-05:**
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05'
```

**2. Write a SQL query to retrieve all transactions where category is 'Clothing' and the quantity sold is more than 10 in the month of Nov-2022**
```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
	AND quantity >= 4
	AND sale_date >= '2022-11-01'
	AND sale_date < '2022-12-01'

-- atau bisa juga

SELECT *
FROM retail_sales
WHERE category = 'Clothing'
	AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
	AND quantity >= 4
```

**3. Write a SQL query to calculate the total sales (total_sale) for each category.:**
```sql
SELECT 
	category,
	SUM (total_sale) as net_sale,
	COUNT(*) as total_orders -- ini tambahan
FROM retail_sales
GROUP BY 1 -- 1 ini merujuk ke kolom pertama yang diselect, kalau di case ini category
```

**4. Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.**
```sql
SELECT 
	ROUND(AVG(age), 2) as average_age -- ROUND tuk membulatkan
FROM retail_sales
WHERE category = 'Beauty'
```

**5. Write a SQL query to find all transactions where the total_sale is greater than 1000.**
```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000
ORDER BY total_sale ASC
```

**6. Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.**
```sql
SELECT 
	category,
	gender,
	COUNT (*) as total_tansactions
FROM retail_sales
GROUP BY 
	category,
	gender
ORDER BY 
	category ASC,
	gender ASC;
```

**7. Write a SQL query to calculate the average sale for each month. Find out best selling month in each year**
```sql
SELECT 
	EXTRACT(YEAR FROM sale_date) AS year,
	EXTRACT(MONTH FROM sale_date) AS month,
	ROUND(AVG(total_sale)) AS average_selling_each_month,
	RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY ROUND(AVG(total_sale)) DESC) AS ranking
FROM retail_sales
GROUP BY 1, 2

-- kalau mau lihat khusus untuk ranking 1 saja / ranking 2 saja, dll saja

SELECT *
FROM (
	SELECT 
		EXTRACT(YEAR FROM sale_date) AS year,
		EXTRACT(MONTH FROM sale_date) AS month,
		ROUND(AVG(total_sale)) AS average_selling_each_month,
		RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY ROUND(AVG(total_sale)) DESC) AS ranking
	FROM retail_sales
	GROUP BY 1, 2
)
WHERE ranking = 1
```

**8. Write a SQL query to find the top 5 customers based on the highest total sales **
```sql
SELECT 
	customer,
	total_sale_per_customer
FROM (
	SELECT 
		DISTINCT(customer_id) as customer,
		SUM (total_sale) as total_sale_per_customer,
		RANK () OVER (ORDER BY SUM (total_sale) DESC) AS ranking
	FROM retail_sales
	GROUP BY 1
	ORDER BY total_sale_per_customer DESC
) as ya
WHERE ranking <= 5

--atau bisa begini

SELECT
	customer_id,
	SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5 -- pakai limit jauh lebih simpel
```

**9. Write a SQL query to find the number of unique customers who purchased items from each category.**
```sql
SELECT 
	category,
	COUNT (DISTINCT customer_id) as jumlah_customer
FROM retail_sales
GROUP BY 1
ORDER BY jumlah_customer DESC
```

**10. Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)**
```sql
WITH hourly_sale -- penggunaan WITH adalah untuk simplify nested select, sebenarnya sama aja sih
AS (
	SELECT *,
		CASE
			WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning' -- ini untuk membuat value baru di kolom shift
			WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
			ELSE 'Evening'
		END as shift
	FROM retail_sales
)
SELECT
	shift,
	COUNT (transactions_id) as total_order
FROM hourly_sale
GROUP BY 1
ORDER BY total_order DESC
```

This project is part of my portfolio :D 
