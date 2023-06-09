# 🌳:fallen_leaf: Case Study #7 : Balanced Tree Clothing Company



<img align= "center" alt= "logo" width= "400" src= "https://8weeksqlchallenge.com/images/case-study-designs/7.png">


* Note : All the details regarding case study and questions are sourced from [here](https://8weeksqlchallenge.com/case-study-7/)



## 📚 Table Of Contents

* [Business Task]()
* [Dataset Used]()
* [Case study Questions and Solutions]()



## 📩 Business Task

Balanced Tree is a company that provides optimised range of clothing and lifestyle wear for modern world adventurer. :jeans:


Danny, the CEO of this trendy fashion company has asked you to assist the team’s merchandising teams analyse their sales performance and
generate a basic financial report to share with the wider business.



## 🗞️ Dataset Used

For this case study there is a total of 4 datasets for this case study. 
however you will only need to utilise 2 main tables to solve all of the regular questions, and the additional 2 tables are used only for
the bonus challenge question!

Table 1: `Product Details` includes all information about the entire range that Balanced Clothing sells in their store.

Table 2: `Product Sales` contains product level information for all the transactions made for Balanced Tree including quantity, price,
          percentage discount, member status, a transaction ID and also the transaction timestamp.

Table 3: `Product Hierarcy `

Table 4: `Product prices` contains all details of product price and product id.


## 🧩 Case Study Questions And Solutions


## 📊 A. High Level Sales Analysis

Q1. What was the total quantity sold for all products?
Q2. What is the total generated revenue for all products before discounts?
Q3. What was the total discount amount for all products?




## ☎️ Transaction Analysis

Q1. How many unique transactions were there?

Q2. What is the average unique products purchased in each transaction?

Q3. What are the 25th, 50th and 75th percentile values for the revenue per transaction?

Q4. What is the average discount value per transaction?

Q5. What is the percentage split of all transactions for members vs non-members?

Q6. What is the average revenue for member transactions and non-member transactions?



## 👚🧦 Product Analysis

Q1. What are the top 3 products by total revenue before discount?

Q2. What is the total quantity, revenue and discount for each segment?

Q3. What is the top selling product for each segment?

Q4. What is the total quantity, revenue and discount for each category?

Q5. What is the top selling product for each category?

Q6. What is the percentage split of revenue by product for each segment?

Q7. What is the percentage split of revenue by segment for each category?

Q8. What is the percentage split of total revenue by category?

Q9. What is the total transaction “penetration” for each product? (hint: penetration = number of transactions where at least 1 quantity
    of a product was purchased divided by total number of transactions)
    
Q10. What is the most common combination of at least 1 quantity of any 3 products in a 1 single transaction?



## 🔖 Reporting Challenge

Write a single SQL script that combines all of the previous questions into a scheduled report that the Balanced Tree team can run at the beginning
of each month to calculate the previous month’s values.

Imagine that the Chief Financial Officer (which is also Danny) has asked for all of these questions at the end of every month.

He first wants you to generate the data for January only - but then he also wants you to demonstrate that you can easily run the samne 
analysis for February without many changes (if at all).

Feel free to split up your final outputs into as many tables as you need - but be sure to explicitly reference which table outputs relate
to which question for full marks.



## 🎁 Bonus Challenge

Use a single SQL query to transform the `product_hierarchy` and `product_prices` datasets to the `product_details` table.

Hint: you may want to consider using a recursive CTE to solve this problem!
















