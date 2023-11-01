# Danny's Diner

![Danny's Diner](https://github.com/CodeofRahul/Danny-s-Diner/assets/143285125/4cfa9c25-d267-4794-b8d9-71b14adc6eba)

## Introduction

Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.

Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business.

## Problem statement

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they've spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers. He plans on using these insights to help him decide whether he should expand the existing customer loyalty program.

## Datasets

Three key datasets for this case study

- sales: The sales table captures all customer_id level purchases with an corresponding order_date and product_id information for when and what menu items were ordered.
- menu: The menu table maps the product_id to the actual product_name and price of each menu item.
- members: The members table captures the join_date when a customer_id joined the beta version of the Danny's Diner loyalty program.

## Case Study Questions

1. What is the total amount each customer spent at the restaurant?
2. How many days has each customer visited the restaurant?
3. What was the first item from the menu purchased by each customer?
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
5. Which item was the most popular for each customer?
6. Which item was purchased first by the customer after they became a member?
7. Which item was purchased just before the customer became a member?
8. What is the total items and amount spent for each member before they became a member?
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### 1. What is the total amount each customer spent at the restaurant?

```sql

SELECT s.customer_id, SUM(m.price) as "Total amount"
FROM sales AS s
INNER JOIN menu AS m
ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id ASC;

```

##### Result

![question 1](https://github.com/CodeofRahul/Danny-s-Diner/assets/143285125/148ea2b4-5e50-4f7d-9898-524104577b16)

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### 2.	How many days has each customer visited the restaurant?

```sql

SELECT customer_id, count(DISTINCT order_date) as "visit count"
FROM sales
GROUP BY customer_id
ORDER BY customer_id ASC;

```

##### Result

![question 2](https://github.com/CodeofRahul/Danny-s-Diner/assets/143285125/528a1273-6aba-436d-bd09-46122ccccba6)

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### 3.	What was the first item from the menu purchased by each customer?

```sql

WITH first_rank as(
SELECT s.customer_id, s.order_date, m.product_name,
dense_rank() over(partition by s.customer_id order by s.order_date) as rank
from sales as s
inner join menu as m
on s.product_id = m.product_id)

SELECT customer_id, product_name
FROM first_rank
WHERE rank = 1
GROUP BY customer_id, product_name;

```

##### Result

![question 3](https://github.com/CodeofRahul/Danny-s-Diner/assets/143285125/357a1a58-98e0-47c0-97ea-47223454948d)

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### 4.	What is the most purchased item on the menu and how many times was it purchased by all customers?

```sql

SELECT m.product_name as "most purchased item", count(s.product_id) "purchase count"
FROM sales as s
INNER JOIN menu as m
ON s.product_id = m.product_id
GROUP BY m.product_name
ORDER BY count(s.product_id) DESC
LIMIT 1;

```

##### Result

![question 4](https://github.com/CodeofRahul/Danny-s-Diner/assets/143285125/b09c6738-1315-4867-bb68-c86040444731)

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### 5.	Which item was the most popular for each customer?

```sql

with popular as (
	SELECT s.customer_id, m.product_name, count(s.product_id) as product_count,
	dense_rank() over(partition by s.customer_id order by count(s.product_id) desc) as rank
	FROM sales as s
	INNER JOIN menu as m
	ON s.product_id = m.product_id
	group by s.customer_id, m.product_name)
	
SELECT customer_id, product_name, product_count
FROM popular
WHERE rank = 1;

```

##### Result

![question 5](https://github.com/CodeofRahul/Danny-s-Diner/assets/143285125/a4d68fa9-5c6a-403c-a4aa-65b01dece995)

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### 6.	Which item was purchased first by the customer after they became a member?

```sql

with first_purchase as (
	SELECT s.customer_id, s.product_id,
	row_number() over(partition by s.customer_id order by s.order_date) as row_num
	FROM sales as s
	INNER JOIN members as b
	ON S.customer_id = b.customer_id and
	s.order_date > b.join_date
)

SELECT customer_id, product_name
FROM first_purchase as f
INNER JOIN menu as m
ON f.product_id = m.product_id
WHERE row_num = 1
ORDER BY customer_id asc

```

##### Result

![question 6](https://github.com/CodeofRahul/Danny-s-Diner/assets/143285125/864a6da0-bc2b-4403-8db4-3e3c9577a11e)

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### 7.	Which item was purchased just before the customer became a member?

```sql

WITH purchased as
(SELECT s.customer_id, m.product_name,
dense_rank() over(partition by s.customer_id order by s.order_date desc) as rn
FROM sales as s
INNER JOIN menu as m
ON s.product_id = m.product_id
INNER JOIN members as mb
ON s.customer_id = mb.customer_id
WHERE s.order_date < mb.join_date)

SELECT customer_id, product_name
FROM purchased
WHERE rn = 1;

```

##### Result

![question 7](https://github.com/CodeofRahul/Danny-s-Diner/assets/143285125/9b718237-dd72-46cd-8943-bd55ddb59d23)

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### 8.	What is the total items and amount spent for each member before they became a member?

```sql

SELECT s.customer_id,
count(m.product_name) as "total items",
concat('$ ', sum(m.price)) as "amount spent"
FROM sales as s
INNER JOIN menu as m
ON s.product_id = m.product_id
INNER JOIN members as mb
ON s.customer_id = mb.customer_id
WHERE s.order_date < mb.join_date
GROUP BY s.customer_id
ORDER BY s.customer_id asc;

```

##### Result

![question 8](https://github.com/CodeofRahul/Danny-s-Diner/assets/143285125/c17383ec-e767-4e7d-a46a-3ba2ac677bd2)

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### 9.	If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

```sql

SELECT s.customer_id,
sum(
CASE
	WHEN m.product_name = 'sushi' THEN m.price * 10 * 2
	ELSE m.price * 10
END
) as points
FROM sales as s
INNER JOIN menu as m
ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id asc;

```

##### Result

![question 9](https://github.com/CodeofRahul/Danny-s-Diner/assets/143285125/c2bbf7f4-b872-4010-9814-4c3352487661)

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### 10.	In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A       and B have at the end of January?

```sql

WITH last_day_cte as (
	SELECT join_date, (join_date + interval '6 days')::date as last_date,
	customer_id
	FROM members
)
SELECT ld.customer_id,
sum(
	CASE
		WHEN order_date between join_date and last_date THEN price*10*2
		WHEN order_date not between join_date and last_date
		AND product_name = 'sushi' THEN price*10*2
		WHEN order_date not between join_date and last_date
		AND product_name not in ('sushi') THEN price*10
	END
) AS customer_points
FROM sales as s
INNER JOIN menu as m
ON s.product_id = m.product_id
INNER JOIN last_day_cte as ld
ON ld.customer_id = s.customer_id
AND order_date <= '2021-01-31'
AND order_date >= join_date
GROUP BY ld.customer_id
ORDER BY ld.customer_id;

```

##### Result

![question 10](https://github.com/CodeofRahul/Danny-s-Diner/assets/143285125/42c83342-5a44-4273-affe-99309ea56a12)

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------
