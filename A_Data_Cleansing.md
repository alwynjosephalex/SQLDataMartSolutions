# 🛒 Case Study #5 - Data Mart

## 🧼 Solution - A. Data Cleansing Steps

In a single query, perform the following operations and generate a new table in the `data_mart` schema named `cleaned_data`:
- Convert the `week_date` to a `DATE` format
- Add a `week_number` as the second column for each `week_date` value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc
- Add a `month_number` with the calendar month for each `week_date` value as the 3rd column
- Add a `calendar_year` column as the 4th column containing either 2018, 2019 or 2020 values
- Add a new column called `age_band` after the original segment column using the following mapping on the number inside the segment value

<img width="166" alt="image" src="https://user-images.githubusercontent.com/81607668/131438667-3b7f3da5-cabc-436d-a352-2022841fc6a2.png">

- Add a new `demographic` column using the following mapping for the first letter in the `segment` values:  

| segment | demographic | 
| ------- | ----------- |
| C | Couples |
| F | Families |

- Ensure all `null` string values with an "unknown" string value in the original `segment` column as well as the new `age_band` and `demographic` columns
- Generate a new `avg_transaction` column as the sales value divided by transactions rounded to 2 decimal places for each record

**Answer:**

## Create New Table `cleaned_data`

Let's construct the structure of `cleaned_data` table and lay out the actions to be taken.

_`*` represent new columns_

| Columns | Actions to take |
| ------- | --------------- |
| week_date | Convert to `DATE` using `TO_DATE`
| week_number* | Extract number of week using `EXTRACT` 
| month_number* | Extract number of month using `EXTRACT` 
| calendar_year* | Extract year using `EXTRACT`
| region | No changes
| platform | No changes
| segment | No changes
| age_band* | Use `CASE WHEN` and based on `segment`, 1 = `Young Adults`, 2 = `Middle Aged`, 3/4 = `Retirees` and null = `Unknown`
| demographic* | Use `CASE WHEN` and based on `segment`, C = `Couples` and F = `Families` and null = `Unknown`
| transactions | No changes
| avg_transaction* | Divide `sales` with `transactions` and round up to 2 decimal places
| sales | No changes

**Answer:**

````sql
WITH raw_data AS
(SELECT * FROM data_mart.weekly_sales),
cleaned_data AS
(SELECT to_date(week_date,'dd/mm/yy') as week_date,extract('week' from to_date(week_date,'dd/mm/yy')) as week_number,extract('month' from to_date(week_date,'dd/mm/yy')) as month_number, extract('year' from to_date(week_date,'dd/mm/yy')) as calendar_year, region, platform,
CASE
WHEN segment = 'null' THEN 'unknown'
WHEN SUBSTRING(segment,2) = '1' THEN 'Young Adults'
WHEN SUBSTRING(segment,2) = '2' THEN 'Middle Aged' ELSE 'Retirees' END AS age_band,
CASE 
WHEN SUBSTRING(segment,1,1) = 'C' THEN 'Couples'
WHEN SUBSTRING(segment,1,1) = 'F' THEN 'Families'
ELSE 'unknown' END 
AS demographic,
ROUND(sales*1.0/transactions,2) AS avg_transaction, customer_type, transactions, sales from raw_data)
SELECT * FROM cleaned_data;
````
<img width="1148" alt="image" src="/images/Q1.png">



