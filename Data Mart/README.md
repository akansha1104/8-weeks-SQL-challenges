# :🛒🛍️  Case Study #5 : Data Mart 



<img align= "center" alt= "logo" width= "400" src= "https://8weeksqlchallenge.com/images/case-study-designs/5.png">


*  Note that all the information regarding dataset and case study is sourced from [here](https://8weeksqlchallenge.com/case-study-5/)




# 📚 Table Of Contents

* [Introduction and Problem Statement](https://github.com/akansha1104/8-weeks-SQL-challenges/tree/main/Data%20Mart#-business-task)
* [Dataset Used](https://github.com/akansha1104/8-weeks-SQL-challenges/tree/main/Data%20Mart#%EF%B8%8F-dataset-used)
* [Case Study Questions and Solutions](https://github.com/akansha1104/8-weeks-SQL-challenges/tree/main/Data%20Mart#-a-data-cleansing-steps)



 ## 📩 Business Task
 
 Danny embarked upon a new venture of online supermarket that specialises in  fresh produce and is named as DATA MART. After running international operations for his online supermarket, Danny is asking for support to analyse his sales performance. 
In June 2020 - large scale supply changes were made at Data Mart. All Data Mart products now use sustainable packaging methods in every single step from the farm all the way to the customer.

Danny needs help to quantify the impact of this change on the sales performance for Data Mart and it’s separate business areas.

The key business question to be answered are following--

* What was the quantifiable impact of the changes introduced in June 2020?
* Which platform, region, segment and customer types were the most impacted by this change?
* What can we do about future introduction of similar sustainability updates to the business to minimise impact on sales?



## 🗞️ Dataset Used
For this case study there is only a single table: data_mart.weekly_sales

The columns are pretty self-explanatory based on the column names but here are some further details about the dataset:



## ✏️ Case Study Questions and solutions

* Note that all the queries are done on `MYSQL`.


## 🧴🧼 A. Data Cleansing Steps
In a single query, perform the following operations and generate a new table in the data_mart schema named `clean_weekly_sales`:

* Convert the week_date to a `DATE` format.
* Add a` week_number` as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be   2 etc.
* Add a `month_number` with the calendar month for each week_date value as the 3rd column.
* Add a `calendar_year` column as the 4th column containing either 2018, 2019 or 2020 values.
* Add a new column called `age_band` after the original segment column using the following mapping on the number inside the segment value

| segment | age-band |
|----------|---------|
| 1    |  young adults|
| 2  |  middle aged |
| 3 or 4 | retirees |

* Add a new `demographic` column using the following mapping for the first letter in the segment values:

| segment  | demographic |
|----------|--------------|
 | C | couples|
 | F | families |
 
* Ensure all `null string` values with an "unknown" string value in the original `segment` column as well as the new `age_band` and `demographic` columns
* Generate a new `avg_transaction` column as the sales value divided by transactions rounded to 2 decimal places for each record.


## solution

Let's construct the structure of `clean_weekly_sales` table and lay out the actions to be taken.

| Column Name | Action to take |
|-------------|-----------------|
|week_date | Convert to `DATE` using `DATE()` |
|week_number*|	Extract number of week using `WEEK` |
|month_number*|	Extract number of month using `MONTH`|
|calendar_year*|	Extract year using `YEAR`|
|region|	No changes|
|platform|	No changes|
|segment|	No changes|
|age_band*|	Use `CASE` statement and apply conditional logic on `segment` with 1 = `Young Adults`, 2 =` Middle Aged`, 3/4 =` Retirees` and null = `Unknown`|
|demographic*|	Use `CASE` `WHEN` and apply conditional logic on based on `segment`, C = `Couples` and F = `Families` and null =` Unknown`|
|transactions	|No changes|
|avg_transaction*|	Divide `sales` with `transactions` and round up to 2 decimal places|
|sales|	No changes|

```sql
DROP TABLE IF EXISTS clean_weekly_sales:
CREATE TEMP TABLE 'clean_weekly_sales' AS (
  SELECT 
        DATE(week_date) AS date ,
        WEEK(week_date) AS week_number ,
        MONTH(week_date) AS month_number ,
        YEAR(week_date)  IN (2018, 2019, 2020) AS calender_year,
        region ,
        platform ,
        segment ,
        CASE 
            WHEN RIGHT(segment , 1) = 1 THEN 'Young Adults'
            WHEN RIGHT(segment , 1) = 2 THEN 'middle Aged'
            WHEN RIGHT(segment , 1) IN (3 , 4) THEN 'Retirees'
            ELSE 'unknown' 
        END) AS Age-band ,
        CASE LEFT(segment , 1)
            WHEN 'C' THEN 'couples'
            WHEN 'F' THEN 'Families'
            ELSE 'unknown'
        END) AS demographic ,
        transactions, 
        ROUND( sales / transactions , 2 ) AS avg_transaction ,
        sales
   FROM  data_mart.weekly_sales
);
```
![image](https://github.com/akansha1104/8-weeks-SQL-challenges/assets/94058322/770bb249-992d-4b13-94d6-3a355b74d910)

------------------------------------------------------------------------------------------------------------------------------------------------------------------ 



## 🛍️ B. Data Exploration
Q1. What day of the week is used for each week_date value?

```sql
SELECT DAYNAME( DISTINCT week_date) AS week_day
FROM clean_weekly_sales;
```

## answer
| week_day|
|---------|
| monday |



Q2. What range of week numbers are missing from the dataset?








Q3. How many total transactions were there for each year in the dataset?

```sql
SELECT calendar_year, 
       SUM(transactions) AS total_transactions
 FROM clean_weekly_sales
 GROUP BY calendar_year
 ORDER BY calendar_year;
``` 
Answer:

|calendar_year|	total_transactions|
|------------|-------------|
|2018	|346406460|
|2019|	365639285|
|2020|	375813651|


Q4. What is the total sales for each region for each month?

```sql
SELECT month_number, 
       region, 
      SUM(sales) AS total_sales
FROM clean_weekly_sales
GROUP BY month_number, region
ORDER BY month_number, region;
```


Q5. What is the total count of transactions for each platform

```sql
SELECT platform, 
       COUNT(transactions) AS total_transactions
FROM clean_weekly_sales
GROUP BY platform;
```

Answer:




Q6. What is the percentage of sales for Retail vs Shopify for each month?
```sql
WITH monthly_transactions AS (
  SELECT 
    calendar_year, 
    month_number, 
    platform, 
    SUM(sales) AS monthly_sales
  FROM clean_weekly_sales
  GROUP BY calendar_year, month_number, platform
)

SELECT 
  
```
Answer:




Q7.What is the percentage of sales by demographic for each year in the dataset?

```sql
WITH cte AS (
              SELECT calender_year , 
              demographic,
              SUM( sales) AS yearly_sales
              FROM clean_weekly_sales
              GROUP BY calender_year;
)
   SELECT calendr_year,
          ROUND( 100* yearly_sales / SUM( yearly_sales) , 2 ) OVER ( PARTITION BY demographic) AS pct_sales_by_demographic
   FROM cte;
   ```
   
              
             
Q8.Which age_band and demographic values contribute the most to Retail sales?

```sql
SELECT age_band, 
       demographic, 
       SUM(sales) AS total_sales
FROM clean_weekly_sales
WHERE platform = 'retail'
GROUP BY age_band , demographic
ORDER BY total_sales DESC;
```
Answer:

|age_band	|demographic|	total_sales|
|-------|---------|--------|
|unknown|	unknown|	16067285533|	
|Retirees|	Families|	66346869

The majority of the highest retail sales are contributed by 'unkown' age_band and 'unknown' demographic .

Q9.Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?

```sql
SELECT  calendar_year, 
        platform, 
        ROUND(AVG(avg_transaction),0) AS avg_transaction_column, 
        SUM(sales) / SUM(transactions) AS avg_transaction
FROM clean_weekly_sales
GROUP BY calendar_year, platform
ORDER BY calendar_year, platform;
```
Answer:

|calendar_year|	platform	|avg_transaction_column	|avg_transaction|
|----------|--------|----------|---------|
|2018|	Retail|	43	|36|
|2018	|Shopify|	188	|192|
|2019|	Retail|	42|	36|
|2019|	Shopify|	178|	183|
|2020|	Retail|	41|	36|
|2020|	Shopify|	175	|179|


For finding the average transaction size for each year by platform accurately, it is recommended to use `avg_transaction`.

------

## 💡 C. Before & After Analysis
This technique is usually used when we inspect an important event and want to inspect the impact before and after a certain point in time.

Taking the week_date value of `2020-06-15` as the baseline week where the Data Mart sustainable packaging changes came into effect.
We would include all week_date values for 2020-06-15 as the start of the period `after` the change and the previous week_date values would be` before`.

Using this analysis approach - answer the following questions:

1. What is the total sales for the 4 weeks before and after 2020-06-15? What is the growth or reduction rate in actual values and percentage of sales?

Before calculating the sales for 4 weeks before and after `2020-06-15` , we need to find out the week_number of this date to use it as filter in our analysis.
```sql
SELECT DISTINCT week_number
FROM clean_weekly_sales
WHERE week_date = '2020-06-15'
AND calender_year = '2020';
```
|week_number|
|------------|
|25|

The week_number of given date is` 25`. Now I created 2 CTEs.
* CTE 1 - to filter the dataset for 4 weeks before and after 2020-06-15 and then claculate the sum of total sales for this period.
* CTE 2 - use `case` statement to filter out the total sales before and after 2020-06-15 specifically for these periods.

```sql
WITH cte1 AS (
               SELECT  week_number, week_date ,
                      SUM( sales) AS total_sales
               FROM clean_weekly_sales
               WHERE (week_number BETWEEN 21 AND 28)
               AND (calendar_year = 2020)
               GROUP BY week_number, week_date
) ,
   cte2 AS (
             SELECT 
                 SUM( CASE 
                         WHEN week_number BETWEEN 21 AND 24 THEN total_sales END ) AS sales_before,
                 SUM(CASE 
                         WHEN week_number BETWEEN 25 AND 28 THEN total_sales END ) AS sales_after,
              FROM cte1
)
   SELECT (sales_after - sales_before) AS sales_variance
   ROUND (100* 
          ( after_sales - before_sales) / before_sales , 2 ) AS percentage_variance
   FROM cte2 ;
```

 Answer:

|sales_variance|	variance_percentage|
|----------|----------|
|-26884188|	-1.15|

Since the implementation of new sustainable packaging practices , there has been a decrease in sales amounting to $26,884,188 reflecting a negative change by 1.15%. Introducing new packaging not always guarantee a positive impact as customers may not recognise the products on shelves in supermarkets due to change in packaging.

---------------------------------------------------------------------------------------------------------------------------------------------------------------                      
2. What about the entire 12 weeks before and after? 
In this question we can apply similar approach as in the earlier question. Here instead of 4 weeks before and after, we have to calculate sales for 12 weeks before and after.

```sql
WITH cte1 AS (
              SELECT week_date ,
                    week_number,
                    SUM(sales ) AS total_sales
                    FROM clean_weekly_sales
                    WHERE (week_number BETWEEN 13 AND 37)
                    AND calender_year = 2020)
                    GROUP BY week_date, week_number
 ) , cte2 AS (
               SELECT 
                   SUM ( CASE 
                           WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_packaging_sales
                   SUM ( CASE 
                           WHEN week_number BETWEEN 25 AND 37 THEN total_sales END) AS after_packaging_sales
                FROM cte1
  )
     SELECT (after_packaging_sales - before_packaging_sales) AS sales_variance
     ROUND ( 100*
              (after_packaging_sales - before_packaging_sales) / before_packaging_sales , 2 ) AS percentage_variance
      FROM cte2;
  ```
  Answer:

|sales_variance|	variance_percentage|
|-----------|-----------|
|-152325394	|-2.14|

It seems like sales have further declined now at negative 2.14% !! This sustainable packaging idea doesnt sounds good for datamart customers and Danny too!!

------------------------------------------------------------------------------------------------------------------------------------------------------------------  
3 How do the sale metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?

I'm dividing  this question in two parts --

Part 1. How do the sale metrics for 4 weeks before and after compare with the previous years in 2018 and 2019?
* Here,  the question is asking us to find the sales variance between 4 weeks before and after '2020-06-15' for years 2018, 2019 and 2020. 
* We can apply the same solution as above and add `calendar_year` and group by the results with `calendar_year` to get the sales variance for 2018, 2019 along with 2020.
```sql
WITH cte1 AS (
  SELECT 
    calendar_year,
    week_number, 
    SUM(sales) AS total_sales
  FROM clean_weekly_sales
  WHERE week_number BETWEEN 21 AND 28
  GROUP BY calendar_year, week_number
)
, cte2 AS (
  SELECT 
    calendar_year,
    SUM(CASE 
      WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_packaging_sales,
    SUM(CASE 
      WHEN week_number BETWEEN 25 AND 28 THEN total_sales END) AS after_packaging_sales
  FROM cte1
  GROUP BY calendar_year
)

SELECT 
  calendar_year, 
  after_packaging_sales - before_packaging_sales AS sales_variance, 
  ROUND(100 * 
    (after_packaging_sales - before_packaging_sales) 
    / before_packaging_sales,2) AS variance_percentage
FROM cte2;
```

Answer:

|calendar_year|	sales_variance|	variance_percentage|
|-------|--------|--------|
|2018|	4102105|	0.19|
|2019	|2336594|	0.10|
|2020	|-26884188	|-1.15|

In 2018, there was a sales variance of $4,102,105, with a positive variance of 0.19% indicating that sales have increased after the change period.
similarly, for 2019 , the sales variance is $2336594 with variance as 0.10% also indicating postive results after change period.

However, looking at results of 2020, there was negative sales variance of 1.15% amounting to $26884188, which shows that there was decrease in sales after the new sustainable packaging idea was introduced. It seems that this new packaging was not a good idea for customers and Danny too. 
These types of changes like , changing the packaging of products , does not always bring positive results as may be it becomes difficult for customers to recognise the products on the shelves of mart and purchase some other brand products of same category.

Part 2: How do the sale metrics for 12 weeks before and after compare with the previous years in 2018 and 2019?

For this part of question we will use the same logic as above introducing calendar_year and group by the results with it. The only difference is now the weeks will be from 13 to 24 for before period and week 25 to 37 for after period.

```sql
WITH cte1 AS (
  SELECT 
    calendar_year, 
    week_number, 
    SUM(sales) AS total_sales
  FROM clean_weekly_sales
  WHERE week_number BETWEEN 13 AND 37
  GROUP BY calendar_year, week_number
)
, cte2 AS (
  SELECT 
    calendar_year,
    SUM(CASE 
      WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_packaging_sales,
    SUM(CASE 
      WHEN week_number BETWEEN 25 AND 37 THEN total_sales END) AS after_packaging_sales
  FROM cte1
  GROUP BY calendar_year
)

SELECT 
  calendar_year, 
  after_packaging_sales - before_packaging_sales AS sales_variance, 
  ROUND(100 * 
    (after_packaging_sales - before_packaging_sales) 
    / before_packaging_sales,2) AS variance_percentage
FROM cte2;
```
Answer:

|calendar_year|	sales_variance	|variance_percentage|
|--------|--------|--------|
|2018	|104256193|	1.63|
|2019	|-20740294	|-0.30|
|2020	|-152325394|	-2.14|

Now here , comparing the sales performance for all three years ,  2020 comes out to be the worst year and 2018 to be best among them. The sales variance for both 2019 and 2020 shows negative results indicating that sales have decreased 12 weeks before and after period for these years. After the introduction of sustainable packaging idea the sales in year 2020 substantially decreased by 2.14% amounting to $152,325,394 which is a huge loss for datamart.
``Danny needs to hire a good `data analyst` who can analyse the sales performance and come up with new ideas to increase the sales of datamart.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------

## 🎁 D. Bonus Question

Which areas of the business have the highest negative impact in sales metrics performance in 2020 for the 12 week before and after period?
`region`
`platform`
`age_band`
`demographic`
`customer_type`

For this question we have to apply the same logic for 12 weeks before and after, this time we have to group the results by region, platform, age_band , demographic and customer_type seperately for year 2020. So I am breaking this question in 5 parts.

Part 1 :  How do the sale metrics performance in 2020  have impacted `region` for 12 weeks before and after ? 

```sql
WITH packaging_sales AS (
  SELECT 
    week_date, 
    week_number,
    region,
    SUM(sales) AS total_sales
  FROM clean_weekly_sales
  WHERE (week_number BETWEEN 13 AND 37) 
    AND (calendar_year = 2020)
  GROUP BY week_date, week_number, region
)
, before_after_changes AS (
  SELECT 
    SUM(CASE 
      WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_packaging_sales,
    SUM(CASE 
      WHEN week_number BETWEEN 25 AND 37 THEN total_sales END) AS after_packaging_sales
  FROM packaging_sales
)

SELECT 
  after_packaging_sales - before_packaging_sales AS sales_variance, 
  ROUND(100 * 
    (after_packaging_sales - before_packaging_sales) / before_packaging_sales,2) AS variance_percentage
FROM before_after_changes;
```

Part 2 :  How do the sale metrics performance in 2020  have impacted `platform` for 12 weeks before and after ? 

solution for this question will be uploaded soon...!!



 














