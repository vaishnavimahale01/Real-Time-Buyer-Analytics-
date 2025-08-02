CREATE DATABASE dinner;
Use dinner;


CREATE TABLE sales(
	customer_id VARCHAR(1),
	order_date DATE,
	product_id INTEGER
);

INSERT INTO sales
	(customer_id, order_date, product_id)
VALUES
	('A', '2021-01-01', 1),
	('A', '2021-01-01', 2),
	('A', '2021-01-07', 2),
	('A', '2021-01-10', 3),
	('A', '2021-01-11', 3),
	('A', '2021-01-11', 3),
	('B', '2021-01-01', 2),
	('B', '2021-01-02', 2),
	('B', '2021-01-04', 1),
	('B', '2021-01-11', 1),
	('B', '2021-01-16', 3),
	('B', '2021-02-01', 3),
	('C', '2021-01-01', 3),
	('C', '2021-01-01', 3),
	('C', '2021-01-07', 3);

CREATE TABLE menu(
	product_id INTEGER,
	product_name VARCHAR(5),
	price INTEGER
);

INSERT INTO menu
	(product_id, product_name, price)
VALUES
	(1, 'sushi', 10),
    (2, 'curry', 15),
    (3, 'ramen', 12);

CREATE TABLE members(
	customer_id VARCHAR(1),
	join_date DATE
);


INSERT INTO members
	(customer_id, join_date)
VALUES
	('A', '2021-01-07'),
    ('B', '2021-01-09');


-- Questions
-- 1. What is the total amount each customer spent in the restaurant?
select s.customer_id, sum(m.price) as total_amount from sales s inner join menu m on m.product_id = s.product_id
group by s.customer_id;


-- 2. How many days has each customer visited the restaurant?

select customer_id, count(distinct order_date) as visit from sales group by customer_id;



-- 3. What was the first item from the menu purchased by each customer?

WITH customer_first_purchase as  
(select s.customer_id, MIN(s.order_date) as first_purchase from sales s group by s.customer_id)
select cfp.customer_id, cfp.first_purchase, m.product_name from customer_first_purchase cfp 
join sales s on cfp.customer_id = s.customer_id
and s.order_date = cfp.first_purchase
join menu m on m.product_id = s.product_id;



-- 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

select m.product_name, count(*) as total_purchase
from sales s join menu m on s.product_id = m.product_id
group by m.product_name
order by total_purchase desc limit 1;

select * from menu;




-- 5. Which item was the most popular for each customer?

WITH customer_popularity AS (
    SELECT s.customer_id, m.product_name, COUNT(*) AS purchase_count,
        DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY COUNT(*) DESC) AS rnk
    FROM sales s
    INNER JOIN menu m ON s.product_id = m.product_id
    GROUP BY s.customer_id, m.product_name
)
SELECT customer_id, product_name, purchase_count
FROM customer_popularity
WHERE rnk = 1;


-- 6. Which item was purchased first by the customer after they became a member?

with item_purchase as (select s.customer_id, MIN(s.order_date) as first_purchase
from sales s 
join 
members mb
on s.customer_id= mb.customer_id
where mb.join_date <= s.order_date
group by customer_id)

select ip.customer_id, m.product_name from item_purchase ip
join sales s on ip.first_purchase = s.order_date
and ip.first_purchase = s.order_date
join menu m on 
s.product_id = m.product_id;
-- 7. Which item was purchased just before the customer became a member?

with item_last as (select s.customer_id, MAX(s.order_date) as last_purchase
from sales s 
join 
members mb
on s.customer_id= mb.customer_id
where mb.join_date > s.order_date
group by customer_id)

select il.customer_id, m.product_name from item_last il join sales s on il.customer_id = s.customer_id
and il.last_purchase = s.order_date
join menu m on m.product_id = s.product_id;


-- 8. What is the total items and amount spent for each member before they became a member?

select s.customer_id, count(*) as total_items, sum(m.price) as total_spent
from sales s
join 
menu m
on s.product_id = m.product_id
join members mb
on mb.customer_id = s.customer_id
where mb.join_date > s .order_date
group by customer_id
order by customer_id
;



-- 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?


select s.customer_id, sum(
case
	when m.product_name='sushi' then m.price*20
    else m.price*10
    end
) as tota_points
from sales s
join 
menu m
on s.product_id = m.product_id
group by customer_id
;


/* 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - 
how many points do customer A and B have at the end of January?*/

select s.customer_id, sum(
	case
    when s.order_date between mb.join_date and date_add(mb.join_date, interval 7 day)
    then m.price*20
    when m.product_name='sushi' then m.price*20
    else
    m.price*10
end)  as member_points
from
sales s
join
menu m on s.product_id = m.product_id
left join members mb on mb.customer_id = s.product_id
where s.customer_id in ('A','B') and s.order_date <= '2021-01-31'
group by customer_id;
