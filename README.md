--1.	Retrieve the total number of orders placed.
select count(*) from orders;

--2.	Calculate the total revenue generated from pizza sales.
select round(sum(o.quantity*p.price),2) as Revanue
From order_details as o
join pizzas as p on o.pizza_id = p.pizza_id;

--3.	Identify the highest-priced pizza.
select top 1 pizza_types.name, pizzas.price
from pizza_types
join pizzas on pizza_types.pizza_type_id=pizzas.pizza_type_id
order by pizzas.price desc;

--Q4.	Identify the most common pizza size ordered.
select pizzas.size,count(order_details.order_details_id) as order_count
from pizzas
join order_details on pizzas.pizza_id = order_details.pizza_id
group By pizzas.size
order by order_count desc;

ALTER TABLE order_details
ALTER COLUMN quantity int;

--Q5.	List the top 5 most ordered pizza types along with their quantities.*/
select top 5 pizza_types.name,sum(order_details.quantity) as quantity
from pizza_types
join pizzas on pizza_types.pizza_type_id=pizzas.pizza_type_id
join order_details on pizzas.pizza_id = order_details.pizza_id
group By pizza_types.name
order by quantity desc;

--6. Join the necessary tables to find the total quantity of each pizza category ordered.
select pizza_types.category,sum(order_details.quantity) as quantity
from pizza_types
join pizzas on pizza_types.pizza_type_id=pizzas.pizza_type_id
join order_details on pizzas.pizza_id=order_details.pizza_id
group by pizza_types.category
order by quantity desc;

--7. Determine the distribution of orders by hour of the day.
select top 3 datepart(hour,time) as hours,
count(order_id) as counts from orders
group by datepart(hour,time)
order by counts desc;

--8. Join relevant tables to find the category-wise distribution of pizzas.
select count(name), category from pizza_types
group by category
order by category desc;

--9 Group the orders by date and calculate the average number of pizzas ordered per day.
select avg(order_per_day) from
(select orders.date, sum(order_details.quantity) as order_per_day
from orders join order_details
on orders.order_id=order_details.order_id
group by orders.date) as order_quantity;

--10 Determine the top 3 most ordered pizza types based on revenue.
select top 3 pizza_types.name, 
sum(order_details.quantity*pizzas.price) as revanue
from pizza_types
join pizzas on pizzas.pizza_type_id=pizza_types.pizza_type_id
join order_details on order_details.pizza_id=pizzas.pizza_id
group by pizza_types.name
order by revanue desc;

--11Calculate the percentage contribution of each pizza type to total revenue.
select pizza_types.category,
round(sum(order_details.quantity*pizzas.price)/
(select round(sum(o.quantity*p.price),2) as Revanue
From order_details as o
join pizzas as p on o.pizza_id = p.pizza_id)*100,2) as revanue_in_percentage
from pizza_types
join pizzas on pizzas.pizza_type_id=pizza_types.pizza_type_id
join order_details on order_details.pizza_id=pizzas.pizza_id
group by pizza_types.category
order by revanue_in_percentage desc;
