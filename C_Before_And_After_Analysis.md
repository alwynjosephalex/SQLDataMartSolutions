# 🛒 Case Study #5 - Data Mart

## 🧼 Solution - C. Before & After Analysis

This technique is usually used when we inspect an important event and want to inspect the impact before and after a certain point in time.

Taking the `week_date` value of `2020-06-15` as the baseline week where the Data Mart sustainable packaging changes came into effect. We would include all `week_date` values for `2020-06-15` as the start of the period after the change and the previous week_date values would be before.

Using this analysis approach - answer the following questions:

**1. What is the total sales for the 4 weeks before and after `2020-06-15`? What is the growth or reduction rate in actual values and percentage of sales?**

My approach was to determine the week number for the given date, and then work our way to the desired result by adding and subtracting the time interval of 4 weeks to it.

````sql
SELECT DISTINCT week_number
FROM cleaned_data
WHERE week_date = '2020-06-15';
````
<img width="138" alt="image" src="/images/Q3a.png">

Now that we know the week number is 25, we can find the before and after dates in week numbers 21-24 and weeks 25-28.

````sql
WITH sales_between_weeks AS
 (SELECT week_date, week_number, 
    SUM(sales) AS total_sales
  FROM cleaned_data
  WHERE (week_number BETWEEN 21 AND 28) 
    AND (calendar_year = 2020)
  GROUP BY week_date, week_number
),
weeks_before_after AS (
  SELECT 
    SUM(CASE WHEN week_number BETWEEN 21 AND 24 THEN total_sales END) AS before_date,
    SUM(CASE WHEN week_number BETWEEN 25 AND 28 THEN total_sales END) AS after_date
  FROM sales_between_weeks)

SELECT 
  before_date, 
  after_date,  
  ROUND(100 * (after_date - before_date) / before_date,2) AS percentage
FROM weeks_before_after;

````
**Answer:**

<img width="528" alt="image" src="/images/Q3ab.png">

Ever since the sustainable packing changes came into effect, we have seen a reduction in the total sales by 1.15 percent. Now, there may be several reasons why this has happened. Maybe the packing changes led to an increase in the prices and our customers were price sensitive, or, the new packaging may not have been eye-catching to our customers as it previously was.

***

**2. What about the entire 12 weeks before and after?**

Using the same approach as before,

````sql
WITH sales_between_weeks AS (
  SELECT week_date, week_number, 
    SUM(sales) AS total_sales
  FROM cleaned_data
  WHERE (week_number BETWEEN 13 AND 37) 
    AND (calendar_year = 2020)
  GROUP BY week_date, week_number
),
weeks_before_after AS (
  SELECT 
    SUM(CASE WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_date,
    SUM(CASE WHEN week_number BETWEEN 25 AND 37 THEN total_sales END) AS after_date
  FROM sales_between_weeks)

SELECT 
  before_date, 
  after_date,  
  ROUND(100 * (after_date - before_date) / before_date, 2) AS percentage
FROM weeks_before_after;
````

**Answer:**

<img width="582" alt="image" src="/images/Q3b.png">

Examining deeper into the time frame of the data, we notice further reduction in the total sales after the packaging changes, further showing us that the changes didn't really help in selling more product.




