                                                Pizza Runner 🍕 🍔 🍟
 
![image](https://github.com/divyasri293/8-Weeks-SQL-Challenge/assets/52830608/ae78adc8-d95f-4fb1-834e-f7cf2c284bb0)

I was trying to find a challenge in SQL spanning from beginner to advanced levels and I believe the Pizza Runner challenge fits the bill perfectly.

__**Introduction**__

Did you know that over 115 million kilograms of pizza is consumed daily worldwide??? (Well according to Wikipedia anyway…)
Danny was scrolling through his Instagram feed when something really caught his eye - “80s Retro Styling and Pizza Is the Future!”
Danny was sold on the idea, but he knew that pizza alone was not going to help him get seed funding to expand his new Pizza Empire - so he had one more genius idea to combine with it - he was going to Uberize it - and so Pizza Runner was launched!
Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.

__**Available Data**__

Because Danny had a few years of experience as a data scientist - he was very aware that data collection was going to be critical for his business’ growth.
He has prepared for us an entity relationship diagram of his database design but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimize Pizza Runner’s operations.
All datasets exist within the pizza_runner database schema - be sure to include this reference within your SQL scripts as you start exploring the data and answering the case study questions.


__**Table 1: runners**__

The runners table shows the registration_date for each new runner.

**Columns** : runner_id,	registration_date



__**Table 2: customer_orders**__

Customer pizza orders are captured in the customer_orders table with 1 row for each individual pizza that is part of the order.
The pizza_id relates to the type of pizza which was ordered whilst the exclusions are the ingredient_id values which should be removed from the pizza and the extras are the ingredient_id values which need to be added to the pizza.
Note that customers can order multiple pizzas in a single order with varying exclusions and extras values even if the pizza is the same type!
The exclusions and extras columns will need to be cleaned up before using them in your queries.

 **Columns**: order_id, customer_id,	pizza_id,	exclusions,	extras,	order_time
 
	

__**Table 3: runner_orders**__

After each order are received through the system - they are assigned to a runner - however not all orders are fully completed and can be cancelled by the restaurant or the customer.
The pickup_time is the timestamp at which the runner arrives at the Pizza Runner headquarters to pick up the freshly cooked pizzas. The distance and duration fields are related to how far and long the runner had to travel to deliver the order to the respective customer.
There are some known data issues with this table so be careful when using this in your queries - make sure to check the data types for each column in the schema SQL!

**Columns**: order_id,	runner_id,	pickup_time,	distance,	duration,	cancellation


__**Table 4: pizza_names**__

At the moment - Pizza Runner only has 2 pizzas available, Meat Lovers or Vegetarian!

**Columns**: pizza_id,	pizza_name



__**Table 5: pizza_recipes**__

Each pizza_id has a standard set of toppings which are used as part of the pizza recipe.

**Columns**: pizza_id,	toppings



__**Table 6: pizza_toppings**__
This table contains all the topping_name values with their corresponding topping_id value.

**Columns**: topping_id,	topping_name

This case study has LOTS of questions - they are broken up by area of focus including:

1. Pizza Metrics
2. Runner and Customer Experience
3. Ingredient Optimisation
4. Pricing and Ratings

**The questions along the commands (answers) are provided in the PizzaRunner file.**



