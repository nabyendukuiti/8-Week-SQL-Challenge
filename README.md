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
```
	create database dannys_diner;

	use dannys_diner;
```
*Create Sales table*
```
	create table sales (
	customer_id varchar(2),
	order_date date,
	product_id int
	);
```
*Inserting data into Sales table*
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
````
*Create Members table*
````
	create table members (
	customer_id varchar(2),
	join_date date,
	primary key (customer_id)
	);
````
*Inserting data into Members table*
````
	insert into members (customer_id,join_date)
	values
	    ('A','2021-01-07'),
            ('B','2021-01-09');
````
*Create Menu table*
````
	create table menu (
	product_id int,
	product_name varchar(5),
	price int,
	primary key (product_id)
	);
````
*Inserting data into Menu table*
	insert into menu (product_id,product_name,price)
	values
	    (1,'Sushi',10),
	    (2,'Curry',15),
	    (3,'Ramen',12);
```

## Question and Solution


