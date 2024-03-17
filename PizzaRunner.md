

--------------------------------
-- # Cleaning Data
--------------------------------
-- 1. Dropping/Trimming irrelavant data from columns

```
Select 
    order_id, 
    runner_id, 
    pickup_time,
    CASE 
        when distance like '%km' THEN Replace(distance, 'km', NULL)
        when distance like '%miles' THEN Replace(distance, 'miles', NULL)
        ELSE distance 
    END AS distance,
    CASE 
        when duration like '%minutes' THEN Replace(duration, 'minutes', NULL)
        when duration like '%mins' THEN Replace(duration, 'mins', NULL)
        when duration like '%minute' THEN Replace(duration, 'minute', NULL)
        ELSE duration 
    END AS duration,
    cancellation 
FROM 
    runner_orders;
```

-----------------------------------------------
-- 2. Handling missing/incorrect data

```
update runner_orders 
set cancellation = NULLIF(cancellation,'');

update runner_orders 
set cancellation = NULLIF(cancellation,'null');

update customer_orders 
set exclusions = NULLIF(exclusions,'');

update customer_orders 
set exclusions = NULLIF(exclusions,'null');

update customer_orders 
set extras = NULLIF(extras,'');

update customer_orders 
set extras = NULLIF(extras,'null');
```
 
--------------------------------------------
-- 3. Correcting the Data Types of columns

```
Alter table runner_orders
	Alter column pickup_time DATETIME NULL
	Alter column duration DECIMAL(5,3) NULL
	Alter column distance int NULL
```
----------------------------------------

-------------------------------------------------
Case Study A: Pizza Metrics
-------------------------------------------------
-- # How many pizzas were ordered?

```
select count(order_id) as num_of_pizzas from customer_orders;
```
![image](https://github.com/divyasri293/8-Weeks-SQL-Challenge/assets/52830608/0bd24fa6-9da2-442b-9d45-5e74fd91eb1e)

------------------------------------------------------------------------------------------- 
-- # How many unique customer orders were made?

```
select count(distinct order_id)as unique_pizzas from customer_orders;
```
![image](https://github.com/divyasri293/8-Weeks-SQL-Challenge/assets/52830608/eb8898d1-846e-46d9-92f7-ded587c489fe)

-------------------------------------------------------------------------------------------
-- # How many successful orders were delivered by each runner?
                 
```
SELECT
	runner_id,
	COUNT(order_id) AS successful_orders
FROM runner_orders
WHERE cancellation IS NULL
GROUP BY runner_id;
```
![image](https://github.com/divyasri293/8-Weeks-SQL-Challenge/assets/52830608/3e5cf66f-4a7f-44c2-9952-26716764c9c5)

-------------------------------------------------------------------------------------------
-- # How many of each type of pizza was delivered?

```
select 
	piz.pizza_name, 
    count(cus.pizza_id) as pizza_count 
from customer_orders cus
	join pizza_names piz 
    on cus.pizza_id = piz.pizza_id
    join runner_orders run
    on cus.order_id = run.order_id
	where run.cancellation IS NULL
	group by piz.pizza_name;
 ```
![image](https://github.com/divyasri293/8-Weeks-SQL-Challenge/assets/52830608/ad0b4517-eb3a-452b-9920-29593b1c2028)

-------------------------------------------------------------------------------------------	
-- # How many Vegetarian and Meatlovers were ordered by each customer?

```
select 
	cus.customer_id,piz.pizza_name, count(cus.pizza_id) as Number_of_Pizzas
from customer_orders cus
join pizza_names piz
on cus.pizza_id = piz.pizza_id
group by cus.customer_id,piz.pizza_name;
```
![image](https://github.com/divyasri293/8-Weeks-SQL-Challenge/assets/52830608/7d56d253-5a67-41ce-b695-dd0f35f09a50)
	
alternative:
```	
SELECT
	customer_id,
    	SUM(CASE WHEN pizza_id = 1 THEN 1 ELSE 0 END) as Meat_Lovers,
		SUM(CASE WHEN pizza_id = 2 THEN 1 ELSE 0 END) as Vegetarian
FROM customer_orders
GROUP BY customer_id;
```
-------------------------------------------------------------------------------------------
-- # What was the maximum number of pizzas delivered in a single order?

```
select order_id, max_pizzas from(
  select 
	cus.order_id, count(cus.pizza_id) as max_pizzas from customer_orders cus
    join runner_orders run 
    on cus.order_id = run.order_id
    where run.cancellation ISNULL
	group by cus.order_id) as e
order by max_pizzas DESC
limit 1;
```
![image](https://github.com/divyasri293/8-Weeks-SQL-Challenge/assets/52830608/c82ac4e2-2144-4ec1-99e1-f3903f049b91)

-------------------------------------------------------------------------------------------
-- # For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

```
select cus.customer_id,
	SUM(CASE when cus.exclusions Is NOT NULL OR cus.extras IS NOT NULL THEN 1 ELSE 0 END) AS changed,
    SUM(CASE when cus.exclusions IS NULL AND cus.extras IS NULL THEN 1 ELSE 0 END) AS no_changes
  FROM customer_orders cus
  JOIN runner_orders run
  on cus.order_id = run.order_id
  where run.distance is not NULL
  GROUP BY customer_id;
```

![image](https://github.com/divyasri293/8-Weeks-SQL-Challenge/assets/52830608/8f8dc8ce-79f2-4935-9ae2-9b818a5101a9)


-------------------------------------------------------------------------------------------  
-- # How many pizzas were delivered that had both exclusions and extras?

```
SELECT COUNT(pizza_id) AS no_of_changed_pizzas
FROM customer_orders
WHERE exclusions IS NOT null AND extras IS NOT null;
```
![image](https://github.com/divyasri293/8-Weeks-SQL-Challenge/assets/52830608/c48f40c8-23ac-4b0b-bcf4-5137ea181386)

-------------------------------------------------------------------------------------------
-- # What was the total volume of pizzas ordered for each hour of the day?

```
SELECT 
	HOUR(order_date) AS hour_of_the_day,
	COUNT(order_id) AS no_of_pizzas
FROM customer_orders
WHERE order_date != ' '
GROUP BY hour_of_day
ORDER BY hour_of_day;
```

-------------------------------------------------------------------------------------------
-- # What was the volume of orders for each day of the week?

```
SELECT 
	DAYNAME(order_date) AS hour_of_the_day,
	COUNT(order_id) AS no_of_pizzas
FROM customer_orders
WHERE order_date != ' '
GROUP BY hour_of_day
ORDER BY hour_of_day;
```

-------------------------------------------------------------------------------------------
-- # How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

```
select 
	week(registration_date) as week_of_the_month, count(runner_id) as runners_registered
from runners
group by week_of_the_month;
```

-------------------------------------------------------------------------------------------
--What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

```
select run.runner_id, AVG(TIMESTAMPDIFF(MINUTE,cus.order_time,run.pickup_time)) as average_time 
from runner_orders run join customer_orders cus
on run.order_id = cus.order_id
where run.pickup_time is not NULL
group by run.runner_id;
```

---------------------------------------------------------------------------------------

Is there any relationship between the number of pizzas and how long the order takes to prepare?

```
with cte as
(
 select cus.order_id, count(pizza_id) as number_of_pizzas, TIMESTAMPDIFF(MINUTE,cus.order_time,run.pickup_time) as time_to_prepare
from runner_orders run join customer_orders cus
on run.order_id = cus.order_id
group by cus.order_id) 
select number_of_pizzas, avg(time_to_prepare) as order_time 
from cte
group by number_of_pizzas ;
```
-------------------------------------------------------------------------------------------

What was the average distance travelled for each customer?
```
select
	cus.customer_id, round(avg(distance),2) as avg_distance_travelled
from runner_orders run join customer_orders cus
on run.order_id = cus.order_id
group by cus.customer_id;
```

-------------------------------------------------------------------------------------------
What was the difference between the longest and shortest delivery times for all orders?
```
SELECT MAX(duration + 0)- MIN(duration + 0) AS difference FROM runner_orders
WHERE (duration + 0) is not NULL;
```

-------------------------------------------------------------------------------------------

What was the average speed for each runner for each delivery and do you notice any trend for these values?
```
select
	run.order_id, run.runner_id, round(avg(distance/duration)*60,2)
from runner_orders run
group by run.order_id, run.runner_id
```

-------------------------------------------------------------------------------------------

What is the successful delivery percentage for each runner?

```
select
	 runner_id, (sum(case when cancellation is NULL then 1 else 0 end)/count(order_id))*100 as delivery_percentage
from runner_orders
group by runner_id
```
