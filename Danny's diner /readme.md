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

-----------------------------------------------------------


##✏️ Case Study Questions and Solutions

### Q1. What is the total amount each customer spent at the restaurant?

```sql
 SELECT s.customer_id , 
        SUM ( m.price) AS total_amt_spent
 FROM dannys_diner.sales s
 JOIN dannys_diner.menu m
 USING (product_id)
 GROUP BY s.customer_id ;
```

### Answer :

|customer_id |total_amt_spent|
|-------|--------|
|B|	74|
|C|	36|
|A|	76|

Customer A spent 76$
Customer B spent 74$
Customer C spent 36$

---------------------------------------------------------------



### Q2. How many days has each customer visited the restaurant?

```sql

SELECT customer_id , 
      COUNT (DISTINCT order_date ) AS no_of_days
FROM dannys_diner.sales
GROUP BY customer_id ;
```

### Answer
|customer_id|	no_of_days|
|------|-------|
|A	|4|
|B|	6|
|C|	2|

Customer A visited 4 times.

Customer B visited 6 times. 

Customer C visited 2 times.



------------------------------------------------------------


### Q3. What was the first item from the menu purchased by each customer?

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


### Answer

|customer_id|	first_items_purchased|
|------|--------|
|A|	curry ,sushi|
|B|	curry|
|C	|ramen |


------------------------------------------------------------------------------------


### Q4. What is the most purchased item on the menu and how many times was it purchased by all customers?

```sql
SELECT m.product_name , 
	   COUNT( s.product_id) AS no_of_times_purchased
FROM dannys_diner.sales s
JOIN dannys_diner.menu m
USING (product_id)
GROUP BY m.product_name
ORDER BY no_of_times_purchased DESC
LIMIT 1;
```

### Answer

|product_name|	no_of_times_purchased|
|-------|-------|
|ramen|	8|



*Most popular item among customers is` RAMEN `🍜,  as it was purchased 8 times!!! That ramen must be YUMMY!!*

----------------------------------------------------------------


### Q5. Which item was the most popular for each customer?

```sql
WITH popular_item AS (
  						SELECT s.customer_id , m.product_name,
  						DENSE_RANK()OVER(PARTITION BY s.customer_id ORDER BY COUNT( s.product_id) DESC ) AS rn
  						FROM dannys_diner.sales s
  						JOIN dannys_diner.menu m
  						USING (product_id)
  						GROUP BY s.customer_id , m.product_name
  )
  	SELECT customer_id , STRING_AGG(product_name , ' ,') AS products_purchased
    FROM popular_item
    WHERE rn = 1
    GROUP BY customer_id;
```



### Answer


|customer_id	|products_purchased|
|-------|-------|
|A|	ramen|
|B	|ramen ,curry ,sushi|
|C|	ramen|




*Customer A and B loved ramen, while customer B seems to enjoy all the three dishes on menu... TRUE FOODIE🍛|||*


---------------------------------------------------------------------------




### Q6. Which item was purchased first by the customer after they became a member?



```sql
WITH first_purchased_item AS ( 
  								SELECT s.customer_id, m.product_name,
  								DENSE_RANK()OVER(PARTITION BY s.customer_id ORDER BY s.order_date) AS rn
  								FROM dannys_diner.sales s 
  								JOIN dannys_diner.menu m 
  								USING (product_id)
  								JOIN dannys_diner.members mb
  								USING (customer_id)
  								WHERE s.order_date > mb. join_date
  								GROUP BY s.customer_id , m.product_name, s.order_date
  )
  	SELECT customer_id , STRING_AGG( product_name , ' ,') AS first_purchased_items
    FROM first_purchased_item
    WHERE rn = 1
    GROUP BY customer_id;
```



### Answer

|customer_id|	first_purchased_items|
|-------|--------|
|A|	ramen|
|B|	sushi|



*After becoming members customer A ordered ramen🍜 , while customer B ordered sushi🍣...!!!*


-------------------------------------------------------




### Q7. Which item was purchased just before the customer became a member?



```sql
WITH first_purchased_item AS ( 
  								SELECT s.customer_id, m.product_name,
  								DENSE_RANK()OVER(PARTITION BY s.customer_id ORDER BY s.order_date DESC) AS rn
  								FROM dannys_diner.sales s 
  								JOIN dannys_diner.menu m 
  								USING (product_id)
  								JOIN dannys_diner.members mb
  								USING (customer_id)
  								WHERE s.order_date < mb. join_date
  								GROUP BY s.customer_id , m.product_name, s.order_date
  )
  	SELECT customer_id , STRING_AGG( product_name , ' ,') AS first_purchased_items
    FROM first_purchased_item
    WHERE rn = 1
    GROUP BY customer_id;
```



### Answer


|customer_id|	first_purchased_items|
|-------|------|
|A	|curry ,sushi|
|B|	sushi|


---------------------------------------------------------------------------------




### Q8. What is the total items and amount spent for each member before they became a member?



```sql
SELECT s.customer_id , 
		COUNT (s.product_id)OVER(PARTITION BY s.customer_id) AS no_of_items ,
        SUM( m.price)OVER(PARTITION BY s.customer_id) AS total_amt_spent
FROM dannys_diner.sales s
JOIN dannys_diner.menu m
USING (product_id)
JOIN dannys_diner.members mb
USING (customer_id)
WHERE s.order_date < mb.join_date;
```



### Answer



|customer_id	|no_of_items|	total_amt_spent|
|-------|-------|-------|
|A|	2|	25|
|B|	3|	40|


--------------------------------------------------------------------------------




### Q9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?



```sql
SELECT s.customer_id ,
		SUM ( CASE WHEN m.product_name = 'sushi' THEN  m.price*20
              ELSE m.price*10
              END ) AS total_points
FROM dannys_diner.sales s
JOIN dannys_diner.menu m
USING (product_id)
GROUP BY s.customer_id
ORDER BY s.customer_id;
```



### Answer



|customer_id	|total_points|
|-------|-------|
|A|	860|
|B|	940|
|C|	360|


-------------------------------------------------------------------------------





### Q10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

```sql



## Bonus Question

The following questions are related creating basic data tables that Danny and his team can use to quickly derive insights without needing to join the underlying tables using SQL.





