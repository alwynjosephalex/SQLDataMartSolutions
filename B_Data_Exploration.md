# 🛒 Case Study #5 - Data Mart

## 🛍 Solution - B. Data Exploration

**Region-wise Analysis**
````sql
SELECT region, SUM(sales) as total_sales
FROM cleaned_data GROUP BY 1
ORDER BY 2 DESC LIMIT 3;
````
**Answer:**

<img width="239" alt="image" src="/images/RegionAnalysis.png">

**Region and Demographic Analysis**
````sql
WITH sales_agg AS 
(SELECT region, age_band, SUM(sales) as total_sales
FROM cleaned_data
GROUP BY 1,2)
SELECT region, age_band, total_sales 
FROM 
(SELECT *, RANK() OVER(PARTITION BY region ORDER BY total_sales DESC)
FROM sales_agg) a 
WHERE rank=1 ORDER BY total_sales DESC;
````
**Answer:**

<img width="641" alt="image" src="/images/RegionDemo.png">


**1. What day of the week is used for each week_date value?**

````sql
SELECT distinct week_date, extract('dow' from week_date) 
as day_of_week FROM cleaned_data ORDER BY 1;
````
**Answer:**

<img width="239" alt="image" src="/images/Q2a.png">

- From this, we observe entries for day of the week = 1 which is Monday.

**2. What range of week numbers are missing from the dataset?**
````sql
SELECT DISTINCT week_number FROM cleaned_data ORDER BY 1; 
````
**Answer:**

- From this, we can see that the weeks 1-12 are missing and weeks 37-57 are missing.

<img width="239" alt="image" src="/images/Q2b.png">

**3. How many total transactions were there for each year in the dataset?**

````sql
SELECT calendar_year,
SUM(transactions) AS total_transactions
FROM cleaned_data
GROUP BY 1 ORDER BY 1;
````

**Answer:**

<img width="318" alt="image" src="/images/Q2c.png">

**4. What is the total sales for each region for each month?**

````sql
SELECT region,month_number,
SUM(sales) as total_sales 
FROM cleaned_data
GROUP BY 1,2
ORDER BY 1,2;
````

**Answer:**

_As there are 7 regions and results came up to 49 rows, I'm only showing solution for AFRICA and ASIA._

<img width="641" alt="image" src="/images/Q2d.png">

**5. What is the total count of transactions for each platform?**

````sql
SELECT platform,
SUM(transactions) as total_transactions
FROM cleaned_data 
GROUP BY 1
ORDER BY 2 DESC;

````

**Answer:**

<img width="319" alt="image" src="/images/Q2e.png">

**6. What is the percentage of sales for Retail vs Shopify for each month?**

````sql
WITH platform_data AS
(SELECT month_number, SUM(
  CASE WHEN
    platform = 'Retail' THEN sales ELSE 0 END) as retail_sales,
 SUM(
  CASE WHEN
    platform = 'Shopify' THEN sales ELSE 0 END) as shopify_sales
 FROM cleaned_data GROUP BY 1),
 monthwise_sales AS (
   SELECT month_number, SUM(sales) AS total_sales FROM cleaned_data GROUP BY 1)
 SELECT c1.month_number,
  round(c1.retail_sales*100.0/total_sales,2) as retail_sales, 
  round(c1.shopify_sales*100.0/total_sales,2) as shopify_sales
  from platform_data c1 
  join monthwise_sales c2 on c1.month_number = c2.month_number
  order by 1;
````

**Answer:**

<img width="628" alt="image" src="/images/Q2f.png">

**7. What is the percentage of sales by demographic for each year in the dataset?**

````sql
WITH demographic_data AS
(SELECT calendar_year, SUM(
  CASE WHEN demographic = 'Couples' THEN sales
  ELSE 0 END) as couples_sales,
 SUM(
  CASE WHEN demographic = 'Families' THEN sales
  ELSE 0 END) as families_sales,
 SUM(
  CASE WHEN demographic = 'unknown' THEN sales
  ELSE 0 END) as unknown_sales
 FROM cleaned_data GROUP BY 1),
 yearwise_sales AS (
   SELECT calendar_year, SUM(sales) AS total_sales
   FROM cleaned_data GROUP BY 1)
 SELECT c1.calendar_year, 
 round(c1.couples_sales*100.0/total_sales,2) as couples_sales, 
 round(c1.families_sales*100.0/total_sales,2) as families_sales, 
 round(c1.unknown_sales*100.0/total_sales,2) as unknown_sales
 from demographic_data c1
 join yearwise_sales c2 on c1.calendar_year = c2.calendar_year order by 1;

````

**Answer:**

<img width="755" alt="image" src="/images/Q2g.png">

**8. Which age_band and demographic values contribute the most to Retail sales?**

````sql
SELECT age_band, demographic,
SUM(CASE WHEN platform = 'Retail' THEN sales ELSE 0 END) AS retail_sales
FROM cleaned_data
GROUP BY 1,2
ORDER BY 3 DESC
LIMIT 1;
````

**Answer:**

<img width="650" alt="image" src="/images/Q2h.png">

The top contributors are unknown, and we’ll have to examine in depth.

**9. Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?**

````sql
SELECT calendar_year,
platform,
ROUND(SUM(sales)/SUM(transactions), 2) AS avg_transaction
FROM cleaned_data
GROUP BY 1,2
ORDER BY 1,2;
````

**Answer:**

<img width="636" alt="image" src="/images/Q2i.png">

We cannot use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify as we would determine the average of averages, which is incorrect and therefore we follow this approach.





