This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data. The project involves setting up a retail sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries. This project is ideal for those who are starting their journey in data analysis and want to build a solid foundation in SQL.

Objectives
Set up a retail sales database: Create and populate a retail sales database with the provided sales data.

Data Cleaning: Identify and remove any records with missing or null values.

Exploratory Data Analysis (EDA): Perform basic exploratory data analysis to understand the dataset.

Business Analysis: Use SQL to answer specific business questions and derive insights from the sales data.

Project Structure

1. Database Setup
Database Creation: The project starts by creating a database named p1_retail_db.
Table Creation: A table named retail_sales is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.****

-- Creating the database
CREATE DATABASE Retail_Sales_Analysis

-- Create Table
CREATE TABLE retail_sales
	(
		transactions_id INT NULL,
        sale_date DATE NULL,
		sale_time TIME NULL,
		customer_id INT NULL,
		gender VARCHAR(10) NULL,
		age INT NULL,
		category VARCHAR(25) NULL,
		quantity INT NULL,
		price_per_unit FLOAT NULL,
		cogs FLOAT NULL,
		total_sale FLOAT NULL
) 

--Data Cleaning as there is nothing null so moving towards next step 

--Basic Questions 

Q1 How many sales we have?
		SELECT COUNT(*) FROM retail_sales AS total_sales

-- Q2 how many unique customers we have?
		SELECT COUNT(DISTINCT customer_id) FROM retail_sales
        
-- Q3 How many category we have and what are they?
		SELECT COUNT(DISTINCT category) AS total_category, 
        GROUP_CONCAT(DISTINCT category) AS category_list
        from retail_sales

-- Data Analysis & Business Key Problems & Answers

-- Q1 Write a SQL query to retrieve all columns for sales made on '2022-11-05'
		SELECT * FROM retail_sales
		WHERE sale_date = '2022-11-05'
    
-- Q2  Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 3 in the month of Nov-2022:
		SELECT * FROM retail_sales
		WHERE category = "Clothing"
		AND
		quantity >= 4
		AND
		sale_date BETWEEN '2022-11-01' AND '2022-11-30'
		ORDER BY sale_date ASC
    
-- Q3 Write a SQL query to calculate the total sales (total_sale) for each category.
		SELECT category,
		SUM(total_sale) AS total_sales
		FROM retail_sales
		GROUP BY category

-- Q4 Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.
		 SELECT category, round(avg (age),2) as avg_age
		 from retail_sales
		 WHERE
		 category= "Beauty"

-- Q5  Write a SQL query to find all transactions where the total_sale is greater than 999.
		 SELECT
		 *
		 FROM retail_sales
		 WHERE total_sale >= 999
		 ORDER BY transactions_id
     
-- Q6 Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.
		SELECT
		category, 
		gender, 
		COUNT(transactions_id) AS total_transactions
		FROM retail_sales
		GROUP BY  category, gender
		ORDER BY  category, gender
    
-- Q7 Write a SQL query to calculate the average sale for each month. Find out best selling month in each year
WITH monthly_sales AS (
		SELECT
			YEAR(sale_date) AS sales_year,
			MONTH(sale_date) AS sales_month,
			AVG(total_sale) AS avg_monthly_sales
		FROM retail_sales
		GROUP BY 
			YEAR(sale_date),
			MONTH(sale_date)
),

ranked_sales AS (
    SELECT
        sales_year,
        sales_month,
        avg_monthly_sales,
        DENSE_RANK() OVER (
            PARTITION BY sales_year
            ORDER BY avg_monthly_sales DESC
        ) AS sale_rank
    FROM monthly_sales
)

SELECT
    sales_year,
    sales_month,
    avg_monthly_sales
FROM ranked_sales
WHERE sale_rank = 1
ORDER BY sales_year;

-- Q8 Write a SQL query to find the top 5 customers based on the highest total sales
	SELECT 
	customer_id,
	SUM(total_sale) as total_sale
	FROM retail_sales 
	GROUP BY customer_id
	ORDER BY total_sale DESC
	LIMIT 5 OFFSET 0

-- Q9  Write a SQL query to find the number of unique customers who purchased items from each category
		SELECT 
        category,
        COUNT(DISTINCT(customer_id)) AS customers
        FROM retail_sales
        GROUP BY category
        

-- Q10 Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17): 
	SELECT
		CASE
			WHEN HOUR(sale_time) <12 THEN 'MORNING'
            WHEN HOUR(sale_time) BETWEEN 12 AND 17 THEN 'AFTERNOON'
            ELSE 'EVENING'
		END AS shift,
			COUNT(transactions_id) as total_orders
		FROM retail_sales
        GROUP BY shift
        ORDER BY total_orders DESC
