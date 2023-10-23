# Case Study #1: Danny's Diner
![Danny's Diner pic](https://github.com/nabyendukuiti/8-Week-SQL-Challenge/assets/140970847/64960e4e-2778-4e7d-9f7d-6a2b240aa220)

## Table of Contents

- [Introduction](#Introduction)
- [Problem Statement](#Problem-Statement)
- [Entity Relationship Diagram](#Entity-Relationship-Diagram)
- [Creating Schema and Tables](#Creating-Schema-and-Tables)
- [Question and Solution](#question-and-solution)

N.B: Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-1/). 

***

## Introduction
In early 2021, Danny opened a restaurant called "Danny's Diner," specializing in sushi, curry, and ramen. Now, he needs help using the basic data he's collected to run his restaurant effectively.

## Problem Statement
Danny wants to understand his customers better by analyzing their visiting habits, spending, and favourite menu items. This will help improve customer experience and decide on expanding the loyalty program. Danny shared limited customer data due to privacy concerns. He's provided three vital datasets for the case study:

- Sales
- Menu
- Members

## Entity Relationship Diagram
![image](https://shorturl.at/rx128)

## Creating Schema and Tables
> **Creating Schema**
```
	CREATE DATABASE dannys_diner;

	USE dannys_diner;
```
> **Creating Sales table**
```
	CREATE TABLE sales (
	   customer_id varchar(2),
	   order_date date,
	   product_id int
	);
```
> **Inserting data into Sales table**
```
	INSERT INTO sales (customer_id,order_date,product_id) 
	VALUES
	    ('A','2021-01-01',1),
	    ('A','2021-01-01',2),
	    ('A','2021-01-07',2),
	    ('A','2021-01-10',3),
	    ('A','2021-01-11',3),
	    ('A','2021-01-07',3),
	    ('B','2021-01-01',2),
	    ('B','2021-01-02',2),
	    ('B','2021-01-04',1),
	    ('B','2021-01-11',1),
	    ('B','2021-01-16',3),
	    ('B','2021-02-01',3),
	    ('C','2021-01-01',3),
	    ('C','2021-01-01',3),
	    ('C','2021-01-07',3);
```
> **Creating Members table**
```
	CREATE TABLE members (
	   customer_id varchar(2),
	   join_date date,
	   primary key (customer_id)
	);
```
> **Inserting data into Members table**
```
	INSERT INTO members (customer_id,join_date)
	VALUES
	    ('A','2021-01-07'),
            ('B','2021-01-09');
```
> **Creating Menu table**
```
	CREATE TABLE menu (
	   product_id int,
	   product_name varchar(5),
	   price int,
	   primary key (product_id)
	);
```
> **Inserting data into Menu table**
```
	INSERT INTO menu (product_id,product_name,price)
	VALUES
	    (1,'Sushi',10),
	    (2,'Curry',15),
	    (3,'Ramen',12);
```

## Question and Solution
> **1. What is the total amount each customer spent at the restaurant?**
```
SELECT
  s.customer_id,SUM(m.price) as total_spending
FROM sales s
INNER JOIN menu m USING(product_id)
GROUP BY s.customer_id;
```
#### Answer
	| customer_id | total_spending |
	| ----------- | -------------- |
	| A           | 76             |
	| B           | 74             |
	| C           | 36             |

> **2. How many days has each customer visited the restaurant?**
```
SELECT
  customer_id, COUNT(DISTINCT order_date) as total_visiting
FROM sales
GROUP BY customer_id;
```
#### Answer
	| customer_id | total_visiting |
	| ----------- | -------------- |
	| A           | 4              |
	| B           | 6              |
	| C           | 2              |	

> **3. What was the first item from the menu purchased by each customer?**
```
WITH ranking AS (
SELECT
  s.customer_id,m.product_name,s.product_id,
  DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY s.order_date) AS rnk
FROM sales s
INNER JOIN menu m USING(product_id)
)
SELECT
  customer_id,product_name
FROM ranking
WHERE rnk = 1
ORDER BY customer_id;
```
#### Answer
	| customer_id | product_name | 
	| ----------- | ------------ |
	| A           | curry        | 
	| A           | sushi        | 
	| B           | curry        | 
	| C           | ramen        |

> **4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
```
SELECT
  m.product_name,COUNT(s.product_id) AS most_purchase
FROM sales s
INNER JOIN menu m USING(product_id)
GROUP BY m.product_name
ORDER BY 2 desc
LIMIT 1;
```
#### Answer
	| product_name | most_purchase | 
	| ------------ | ------------- |
	| Ramen        | 8             |

> **5. Which item was the most popular for each customer?**
```
WITH items AS (
  SELECT
     s.customer_id,s.product_id,m.product_name,COUNT(s.product_id) AS total_purchase,
     DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY COUNT(product_id) DESC) AS rnk
  FROM sales s
  INNER JOINmenu m USING(product_id)
  GROUP BYs.customer_id,s.product_id,m.product_name,customer_id
)
SELECT
  customer_id,product_name
FROM items
WHERE rnk = 1; 
```
#### Answer
	| customer_id | product_name | 
	| ----------- | ------------ |
	| A           | ramen        |
	| B           | sushi        |
	| B           | curry        |
	| B           | ramen        |
	| C           | ramen        |

> **6. Which item was purchased first by the customer after they became a member?**
```
WITH firstbuy AS (
  SELECT
    s.customer_id,s.order_date,s.product_id,m2.product_name,m1.join_date,
    dense_rank() OVER (Partition by s.customer_id ORDER BY s.order_date) AS rnk
  FROM sales s
  INNER JOIN members m1 USING(customer_id)
  INNER JOIN menu m2 USING(product_id)
  WHERE s.order_date > m1.join_date
)
SELECT
  customer_id,product_name
FROM firstbuy
WHERE rnk = 1;
```
#### Answer
	| customer_id | product_name |
	| ----------- | ------------ |
	| A           | ramen        |
	| B           | sushi        |

> **7. Which item was purchased just before the customer became a member?**
```
WITH firstbuy AS (
   SELECT 
     s.customer_id,s.order_date,s.product_id,m2.product_name,m1.join_date,
     DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date DESC) AS rnk
   FROM sales s
   INNER JOIN members m1 USING(customer_id)
   INNER JOIN menu m2 USING(product_id)
   WHERE s.order_date < m1.join_date
)
SELECT
   customer_id,product_name
FROM firstbuy
WHERE rnk = 1;
```
#### Answer
	| customer_id | product_name |
	| ----------- | ------------ |
	| A           | sushi        |
	| A	      | curry        |
	| B           | sushi        |

 > **8. What is the total items and amount spent for each member before they became a member?**
```
WITH firstbuy AS (
   SELECT
     s.customer_id,s.order_date,m2.product_name,m1.join_date,COUNT(s.product_id) AS total_COUNT,
		   (m2.price * COUNT(s.product_id)) total_cost,
		   DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY s.order_date DESC) AS rnk
    FROM sales s
    INNER JOIN members m1 USING(customer_id)
    INNER JOIN menu m2 USING(product_id)
    WHERE s.order_date < m1.join_date
    GROUP BY s.customer_id,s.order_date,m2.product_name,m1.join_date,m2.price
)
SELECT
   product_name,sum(total_COUNT) AS total_purchase,sum(total_cost) AS total_amount
FROM firstbuy
GROUP BY product_name;
```
#### Answer
	| product_name | total_purchase | total_amount |
	| ------------ | -------------- |--------------|
	| Sushi        | 2              |  20          |
	| Curry        | 3              |  45          |

> **9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**
```
WITH points AS (
   SELECT *,
     CASE 
       WHEN product_name = 'Sushi' THEN price*20
       ELSE price*10 
       END AS points
   FROM menu
)
SELECT
   s.customer_id,sum(p.points) AS total_points
FROM points p
INNER JOIN sales s USING(product_id)
GROUP BY customer_id;
```
#### Answer
	| customer_id | total_points | 
	| ----------- | ------------ |
	| A           | 860          |
	| B           | 940          |
	| C           | 360          |

> **10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**
```
WITH point_table AS (
  SELECT *,
    CASE
      WHEN s.order_date BETWEEN m2.join_date AND m2.join_date +6 THEN m1.price *20
      WHEN s.order_date NOT BETWEEN m2.join_date AND m2.join_date +6 AND m1.product_id = 1 THEN m1.price *20
      ELSE m1.price * 10
      END AS points
FROM sales s
INNER JOIN menu m1 USING(product_id)
INNER JOIN members m2 USING(customer_id)
)
SELECT
  customer_id,SUM(points) AS total_points
FROM point_table
WHERE MONTH(order_date)=1
GROUP BY customer_id ;
```
#### Answer
	| customer_id | total_points | 
	| ----------- | ------------ |
	| A           | 1370         |
	| B           | 820          |

## Bonus Question
> - PART A: Join All The Things - Recreate the table with: customer_id, order_date, product_name, price, member (Y/N)
  - PART B: Rank All The Things - Danny needs information on product rankings for customers in the loyalty program but expects null ranking values for non-members.
```
WITH cte AS (
  SELECT *,
    CASE
      WHEN order_date>=join_date THEN 'Y'
      ELSE 'N'
    END AS member
  FROM sales s
  LEFT JOIN members m1 USING(customer_id)
  INNER JOIN menu m2 USING(product_id)
  ORDER BY customer_id
)
SELECT
  customer_id,order_date,product_name,price,member,
  CASE
    WHEN member = 'Y' 
    THEN DENSE_RANK() OVER (PARTITION BY customer_id,member ORDER BY order_date )
    ELSE 'null'
  END AS ranking
FROM cte;
```
#### Answer
	customer_id	order_date	product_name	price	member	ranking
	A		2021-01-01	Sushi		10	N	null
	A		2021-01-01	Curry		15	N	null
	A		2021-01-07	Curry		15	Y	1
	A		2021-01-07	Ramen		12	Y	1
	A		2021-01-10	Ramen		12	Y	2
	A		2021-01-11	Ramen		12	Y	3
	B		2021-01-01	Curry		15	N	null
	B		2021-01-02	Curry		15	N	null
	B		2021-01-04	Sushi		10	N	null
	B		2021-01-11	Sushi		10	Y	1
	B		2021-01-16	Ramen		12	Y	2
	B		2021-02-01	Ramen		12	Y	3
	C		2021-01-01	Ramen		12	N	null
	C		2021-01-01	Ramen		12	N	null
	C		2021-01-07	Ramen		12	N	null	
