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
