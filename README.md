Here’s a comprehensive README.md for your SQL project. This document outlines the purpose, setup, and functionality of your project.

🍕 PizzaHut SQL Database Project

This project is a SQL database for analyzing orders, revenue, and trends for a hypothetical Pizza Hut store. The database is designed to track orders, details of the pizzas ordered, and various sales metrics. It includes queries for basic, intermediate, and advanced insights into the data.


📌 Project Overview

This project focuses on creating and querying a Pizza Hut order management system. The primary goals are to:
	•	Track orders and their details.
	•	Analyze sales trends and generate reports.
	•	Identify top-selling pizzas and revenue contributions.

🗃 Database Schema

The project uses the following tables:

1. orders

Column	Type	Constraints
order_id	INT	NOT NULL, Primary Key
order_date	DATE	NOT NULL
order_time	TIME	NOT NULL

2. orders_details

Column	Type	Constraints
orders_details_id	INT	NOT NULL, Primary Key
order_id	INT	NOT NULL (Foreign Key)
pizza_id	TEXT	NOT NULL
quantity	INT	NOT NULL

Other tables like pizzas and pizza_types are used in queries for detailed analysis.

⚙️ Setup Instructions
	1.	Create the Database:

CREATE DATABASE pizzahut;


	2.	Create Tables:
Execute the CREATE TABLE statements in your SQL client or IDE to set up the orders and orders_details tables.
	3.	Load Data:
Populate the tables with sample data before running the queries.

📝 SQL Queries and Descriptions

Basic Queries
	1.	Retrieve the total number of orders:

SELECT COUNT(order_id) FROM orders;


	2.	Calculate total revenue generated:

SELECT 
    ROUND(SUM(orders_details.quantity * pizzas.price), 2) AS total_sales
FROM
    orders_details
JOIN
    pizzas ON pizzas.pizza_id = orders_details.pizza_id;


	3.	Identify the highest-priced pizza:

SELECT 
    pizza_types.name, pizzas.price
FROM
    pizza_types
JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;



Intermediate Queries
	1.	Find the total quantity of each pizza category ordered:

SELECT 
    pizza_types.category,
    SUM(orders_details.quantity) AS quantity
FROM
    pizza_types
JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
JOIN
    orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY quantity DESC;


	2.	Distribution of orders by hour:

SELECT 
    HOUR(order_time), COUNT(order_id)
FROM
    orders
GROUP BY HOUR(order_time);



Advanced Queries
	1.	Percentage contribution of each pizza category to total revenue:

SELECT 
    pizza_types.category,
    ROUND(SUM(orders_details.quantity * pizzas.price) / 
          (SELECT ROUND(SUM(orders_details.quantity * pizzas.price), 2)
           FROM orders_details
           JOIN pizzas ON pizzas.pizza_id = orders_details.pizza_id) * 100, 0) AS revenue
FROM
    pizza_types
JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN
    orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue DESC;



	2.	Cumulative revenue over time:

SELECT 
    order_date,
    SUM(revenue) OVER (ORDER BY order_date) AS cumulative
FROM
    (SELECT orders.order_date,
            SUM(orders_details.quantity * pizzas.price) AS revenue
     FROM orders_details
     JOIN pizzas ON orders_details.pizza_id = pizzas.pizza_id
     JOIN orders ON orders.order_id = orders_details.order_id
     GROUP BY orders.order_date) AS sales;
