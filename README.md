# Case Study #1: Danny's Diner
![Danny's Diner pic](https://github.com/nabyendukuiti/8-Week-SQL-Challenge/assets/140970847/c2e03e34-e37a-4342-b8f1-ea58f0ac3de8)

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
	create database dannys_diner;

	use dannys_diner;
```
> **Creating Sales table**
```
	create table sales (
	   customer_id varchar(2),
	   order_date date,
	   product_id int
	);
```
> **Inserting data into Sales table**
```
	insert into sales (customer_id,order_date,product_id) 
	values
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
	create table members (
	   customer_id varchar(2),
	   join_date date,
	   primary key (customer_id)
	);
```
> **Inserting data into Members table**
```
	insert into members (customer_id,join_date)
	values
	    ('A','2021-01-07'),
            ('B','2021-01-09');
```
> **Creating Menu table**
```
	create table menu (
	   product_id int,
	   product_name varchar(5),
	   price int,
	   primary key (product_id)
	);
```
> **Inserting data into Menu table**
```
	insert into menu (product_id,product_name,price)
	values
	    (1,'Sushi',10),
	    (2,'Curry',15),
	    (3,'Ramen',12);
```

## Question and Solution
> **1. What is the total amount each customer spent at the restaurant?**
```
	select
	   s.customer_id,sum(m.price) as total_spending
	from
	   sales s
	inner join menu m using(product_id)
	group by s.customer_id;
```
#### Explanation
- Used **INNER JOIN** to join Sales and Menu table. This join is based on matching values in the "product_id" column in both tables.
- Used **SUM** to calculate the total spending for each customer by summing the "price" column from the "menu" table.
- The results are grouped by the "customer_id" column from the "sales" table.

#### Answer
	| customer_id | total_spending |
	| ----------- | -------------- |
	| A           | 76             |
	| B           | 74             |
	| C           | 36             |

> **2. How many days has each customer visited the restaurant?**
```
	select
	   customer_id, count(distinct order_date) as total_visiting
	from sales
	group by customer_id;
```
#### Explanation
- **count(distinct order_date)** this part of the query calculate the number of visits for each customer.The **DISTINCT** keyword ensures that only unique values are considered when counting.
- The results are grouped by the "customer_id" column

#### Answer
	| customer_id | total_visiting |
	| ----------- | -------------- |
	| A           | 4              |
	| B           | 6              |
	| C           | 2              |	
