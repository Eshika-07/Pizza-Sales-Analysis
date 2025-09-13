![Pizza Sales Analysis](images/pizza_sales_analysis.png)

# Pizza Sales Analysis – MySQL Project

## Problem Statement
The pizza outlet has been collecting sales data over time, but the management lacks **clear insights** into customer preferences, sales trends, and revenue drivers.  
Without data-driven decision-making, opportunities for **menu optimization, targeted marketing, and operational efficiency** are being missed.

---

## Analysis Approach
This project uses **MySQL** to perform an in-depth analysis of pizza sales data.  
The process involved:
1. **Data Cleaning & Preparation** – Removing inconsistencies, handling missing values, and structuring the dataset.
2. **Exploratory Data Analysis (EDA)** – Using SQL queries to explore sales patterns and customer behavior.
3. **KPI Tracking** – Calculating total revenue, average order value, and sales growth.
4. **Advanced SQL Techniques** – Applying joins, aggregations, subqueries, and window functions for deeper insights.

---

## Solutions to Relevant Questions
The analysis answers key business questions such as:
**1** **The total number of orders placed**
```sql
SELECT count(order_id) as total_orders from orders;
```
**2** **Total revenue generated from pizza sales**
```sql
SELECT 
	ROUND(SUM(order_details.quantity*pizzas.price),2) AS total_sales
FROM order_details 
JOIN pizzas
ON pizzas.pizza_id = order_details.pizza_id;
```
**3** **Highest priced pizza**
```sql
SELECT pizza_types.name, pizzas.price
FROM pizza_types 
JOIN pizzas
ON pizza_types.pizza_type_id=pizzas.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;
```
**4** **Most common pizza size ordered**
```sql
SELECT pizzas.size, COUNTt(order_details.order_details_id) AS order_count
FROM pizzas 
JOIN order_details
ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC;
```
**5** **Top 5 most ordered pizza types along with their quantities**
```sql
SELECT pizza_types.name,
SUM(order_details.quantity) AS quantity
FROM pizza_types 
JOIN pizzas
ON pizza_types.pizza_type_id=pizzas.pizza_type_id
JOIN order_details
ON order_details.pizza_id=pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY quantity DESC
LIMIT 5;
```
**6** **Total qunatity of each pizza category ordered**
```sql
SELECT pizza_types.category,
SUM(order_details.quantity) AS quantity
FROM pizza_types 
JOIN pizzas
ON pizza_types.pizza_type_id=pizzas.pizza_type_id
JOIN order_details
ON order_details.pizza_id=pizzas.pizza_id
GROUP BY pizza_types.category 
ORDER BY quantity DESC;
```
**7** **Distribution of orders by hour of the day**
```sql
SELECT HOUR(order_time) AS hours, COUNT(order_id) AS order_count
FROM orders
GROUP BY hours;
```
**8** **Category wise distribution of pizzas**
```sql
SELECT category, COUNT(name) FROM pizza_types
GROUP BY category;
```
**9** **Average number of pizzas ordered per day**
```sql
SELECT ROUND(AVG(quantity),0) AS avg_pizza_ordered_per_day 
FROM
(SELECT orders.order_date, SUM(order_details.quantity) AS quantity
FROM orders 
JOIN order_details
ON orders.order_id=order_details.order_id
GROUP BY orders.order_date) AS order_quantity;
```
**10** **Top 3 most ordered pizza types based on revenue**
```sql
SELECT pizza_types.name,
SUM(order_details.quantity*pizzas.price) AS revenue
FROM pizza_types 
JOIN pizzas
ON pizzas.pizza_type_id=pizza_types.pizza_type_id
JOIN order_details
ON order_details.pizza_id=pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;
```
**11** **Percentage contribution of each pizza type to total revenue**
```sql
SELECT pizza_types.category,
ROUND(SUM(order_details.quantity*pizzas.price)/
(SELECT ROUND(SUM(order_details.quantity*pizzas.price),2) AS total_sales
FROM order_details
JOIN pizzas 
ON pizzas.pizza_id=order_details.pizza_id)*100,2) AS revenue
FROM pizza_types 
JOIN pizzas
ON pizza_types.pizza_type_id=pizzas.pizza_type_id
JOIN order_details
ON order_details.pizza_id=pizzas.pizza_id
GROUP BY pizza_types.category 
ORDER BY revenue DESC;
```
**12** **Cumulative revenue generated over time**
```sql
SELECT order_date,
SUM(revenue) OVER(ORDER BY order_date) AS cum_revenue
FROM
(SELECT orders.order_date,
SUM(order_details.quantity*pizzas.price) AS revenue
FROM order_details 
JOIN pizzas
ON order_details.pizza_id=pizzas.pizza_id
JOIN orders
ON orders.order_id=order_details.order_id
GROUP BY orders.order_date) AS sales; 
```
**13** **Top 3 most ordered pizza types based on revenue for each pizza category**
```sql
SELECT category,name,revenue 
FROM
(SELECT category,name,revenue,
RANK() OVER(PARTITION BY category ORDER BY revenue DESC) AS rn
FROM
(SELECT pizza_types.category,pizza_types.name,
SUM(order_details.quantity*pizzas.price) AS revenue
FROM pizza_types 
JOIN pizzas
ON pizza_types.pizza_type_id=pizzas.pizza_type_id
JOIN order_details
ON order_details.pizza_id=pizzas.pizza_id
GROUP BY pizza_types.category,pizza_types.name) 
AS a) 
AS b
WHERE rn<=3;
```

---

## Business Insights
From the analysis, the following actionable insights were derived:
- **Top Performers** – Certain premium pizzas consistently generate the highest revenue.
- **Peak Hours** – Sales spike during lunch (12–2 PM) and dinner (6–9 PM), suggesting targeted promotions during these times.
- **Category Trends** – Classic and Supreme categories dominate sales, but niche categories have untapped potential.
- **Customer Loyalty** – A small group of repeat customers contributes significantly to revenue — loyalty programs could boost retention.

---

## Tech Stack
- **Database**: MySQL  
- **Tools**: MySQL Workbench / Command Line  
- **Dataset**: Pizza sales dataset (sample)  




