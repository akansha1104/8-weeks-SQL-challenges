# 🍜 Case Study #1 : Danny's Diner 



<img align= "center" alt="coding" width= "400" src="https://8weeksqlchallenge.com/images/case-study-designs/1.png">



# 📚 Table Of Contents

* [Introduction And Problem Statement]()
* [Case Study Questions solutions]()



## 📩 Introduction and Problem Statement
This case study is about a small restaurant named `DANNY'S DINER` owned by Danny himself. Danny seriously loves Japanese food so in the beginning of 2021, so
he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen. 🍣 🍛 🍜
Danny’s Diner is in need of  assistance to help the restaurant stay afloat -the restaurant has captured some very basic data from their few months
of operation but have no idea how to use their data to help them run the business.

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and
also which menu items are their favourite. He plans on using these insights to help him decide whether he should expand the existing customer loyalty program.


## Case Study Questions and Solutions

Q1. What is the total amount each customer spent at the restaurant?

```sql
 SELECT s.customer_id , 
        SUM ( m.price) AS total_amt_spent
 FROM dannys_diner.sales s
 JOIN dannys_diner.menu m
 USING (product_id)
 GROUP BY s.customer_id ;
```

## Answer :

|customer_id |total_amt_spent|
|-------|--------|
|B|	74|
|C|	36|
|A|	76|



Q2. How many days has each customer visited the restaurant?

```sql

SELECT customer_id , 
      COUNT (DISTINCT order_date ) AS no_of_days
FROM dannys_diner.sales
GROUP BY customer_id ;
```

## Answer
|customer_id|	no_of_days|
|------|-------|
|A	|4|
|B|	6|
|C|	2|


Q3. What was the first item from the menu purchased by each customer?

```sql
WITH first_item AS (
  					SELECT s.customer_id , m.product_name,
  						     	DENSE_RANK()OVER( PARTITION BY s.customer_id ORDER BY s.order_date) AS rnk
  					FROM dannys_diner.sales s 
  					JOIN dannys_diner.menu m
  					USING (product_id)
  )
  	 SELECT customer_id , STRING_AGG(product_name , ' ,') AS first_items_purchased
    FROM first_item
    WHERE rnk = 1
    GROUP BY customer_id;
```
## Answer

|customer_id|	first_items_purchased|
|------|--------|
|A|	curry ,sushi|
|B|	curry|
|C	|ramen |


Q4. What is the most purchased item on the menu and how many times was it purchased by all customers?
Q5. Which item was the most popular for each customer?
Q6. Which item was purchased first by the customer after they became a member?
Q7. Which item was purchased just before the customer became a member?
Q8. What is the total items and amount spent for each member before they became a member?
Q9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
Q10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do         
    customer A and B have at the end of January?


## Bonus Question

The following questions are related creating basic data tables that Danny and his team can use to quickly derive insights without needing to join the underlying tables using SQL.





