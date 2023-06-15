# Capstone-Project-Data-Exploration-with-SQL
How many customers do we have in the data?
SELECT Count(*) FROM customers;

OUTPUT: 795

What was the city with the most profit for the company in 2015?
SELECT o.shipping_city, SUM(od.order_profits) as total_profit FROM orders o JOIN order_details od ON o.order_id=od.order_id WHERE Extract(year FROM o.order_date)= 2015 GROUP BY o.shipping_city ORDER BY total_profit DESC LIMIT 1;

OUTPUT: NEWYORK CITY

Note: Extract(Year FROM datecoulum) le year extract garxa and this has got another alternative as well: Date_Part(‘year’,o.order_date) also works.

3.In 2015, what was the most profitable city's profit?

SELECT o.shipping_city, SUM(od.order_profits) as total_profit FROM orders o JOIN order_details od ON o.order_id=od.order_id WHERE Extract(year FROM o.order_date)= 2015 GROUP BY o.shipping_city ORDER BY total_profit DESC LIMIT 1;

OUTPUT: 14753

How many different cities do we have in the data?
SELECT COUNT(DISTINCT(o.shipping_city)) FROM orders o;

OUTPUT: 531

Show the total spent by customers from low to high.
SELECT c.customer_name, SUM(od.order_sales) as total_spent FROM customers c JOIN orders o ON c.customer_id=o.customer_id JOIN order_details od ON o.order_id=od.order_id GROUP BY customer_name ORDER BY total_spent;

OUTPUT: LIST OF 793 Row

What is the most profitable city in the State of Tennessee?
SELECT o.shipping_city, SUM(od.order_profits) as total_profit FROM orders o JOIN order_details od ON o.order_id=od.order_id WHERE o.shipping_state='Tennessee' GROUP BY o.shipping_city ORDER BY total_profit DESC LIMIT 1;

OUTPUT: LEBANON 83

What’s the average annual profit for that city across all years?
SELECT ROUND(AVG(od.order_profits),2) as Average_annual_profit FROM orders o JOIN order_details od ON o.order_id=od.order_id WHERE o.shipping_city ='Lebanon';

OUTPUT: 27.67

What is the distribution of customer types in the data?
SELECT customer_segment, COUNT(*) as count FROM customers GROUP BY 1;

OUTPU customer_segment count TEXT INT8
Consumer 410 Corporate 237 Home Office 148

What’s the most profitable product category on average in Iowa across all years?
SELECT p.product_category, ROUND(AVG(od.order_profits)::Numeric,2) as Average_annual_profit FROM product p JOIN order_details od ON p.product_id=od.product_id JOIN orders o ON od.order_id=o.order_id WHERE o.shipping_state='Iowa' GROUP BY p.product_category ORDER BY Average_annual_profit DESC LIMIT 1;

OUTPUT: Furniture 130.25

What is the most popular product in that category across all states in 2016?
SELECT p.product_name, COUNT(od.quantity) as Total_quantity_sold FROM product p JOIN order_details od USING(product_id) JOIN orders o USING(order_id) WHERE p.product_category='Furniture' AND Extract(year from o.order_date)='2016' GROUP BY 1 ORDER BY 2 DESC LIMIT 1;

OUTPUT: Product_name total_quantity_sold Bretford CR4500 Series Slim Rectangular Table 4 Deflect-o Glass Clear Studded Chair Mats 4 Atlantic Metals Mobile 3-Shelf Bookcases, Custom color 4 Eldon Expressions Wood Desk Accessories,Oak 4

Which customer got the most discount in the data? (In total amount)
SELECT c.customer_id,c.customer_name, ROUND(Sum(od.order_discount * od.order_sales)::Numeric,2) as Total_discount FROM customers c JOIN orders o USING(customer_id) JOIN order_details od USING(order_id) GROUP BY 1,2 ORDER BY 3 DESC LIMIT 1;

OUTPUT: Customer_id Customer_name total_discount 687 Sean Miller 11988.90

How widely did monthly profits vary in 2018? SELECT Date_trunc('month',order_date)::DATE AS Month, SUM(order_profits) AS monthly_profits, LAG(SUM(order_profits)) OVER ( ORDER BY Date_trunc('month',order_date)::DATE) as Previous_month_profit, SUM(order_profits)- LAG(SUM(order_profits)) OVER (ORDER BY Date_trunc('month',order_date)::DATE) as monthly_profit_variance FROM orders JOIN order_details USING(order_id) WHERE EXTRACT(year FROM order_date)=2018 GROUP BY 1;
OUTPUT:

Month Monthly_profit Pre_month_pro Monthly_dif 2018-01-01 7137 null null 2018-02-01 1612 7137 -5525 2018-03-01 14758 1612 13146

Which order was the highest in 2015?
SELECT order_id, SUM(order_sales) AS total_sale FROM order_details JOIN orders USING(order_id) WHERE EXTRACT(year FROM order_date)=2015 GROUP BY order_id ORDER BY total_sale DESC LIMIT 1;

OUTPUT: Order_id Total_sale CA-2015-145317 23660

What was the rank of each city in the East region in 2015?
SELECT shipping_city, SUM(quantity) as total_quantity_sold, RANK() OVER (ORDER BY SUM(quantity) DESC) FROM orders JOIN order_details USING(order_id) WHERE shipping_region = 'East' AND EXTRACT(year FROM order_date)=2015 GROUP BY 1 ORDER BY 2 DESC;

OUTPUT: Shipping_city total_quantity rank NEW YORK CITY 1708 1 Philadelphia 403 2 Columbus 167 3 Newark 64 4

Display customer names for customers who are in the segment ‘Consumer’ or ‘Corporate.’ How many customers are there in total?
SELECT customer_name, COUNT(*) as Total_customers FROM customers WHERE customer_segment IN ('Consumer','Corporate') GROUP BY 1;

Output: 647 rows (total count) with number of rows

Calculate the difference between the largest and smallest order quantities for product id ‘100.’
SELECT (MAX(quantity)-MIN(quantity)) as Diff FROM order_details JOIN product USING(product_id) WHERE product_id=100;

Output: Diff 4

Calculate the percent of products that are within the category ‘Furniture.’
SELECT COUNT(*)100/(SELECT COUNT() FROM product) AS furniture_percentage FROM product WHERE product_category = 'Furniture';

OUTPUT:

Furniture_percentage 20

Display the number of duplicate products based on their product manufacturer.
SELECT product_manufacturer, count() as num_duplicate FROM product GROUP BY 1 HAVING COUNT()>1;

OUTPUT: 169 rows

Show the product_subcategory and the total number of products in the subcategory. Show the order from most to least products and then by product_subcategory name ascending.
SELECT product_subcategory, count(*) as total_products FROM product GROUP BY product_subcategory ORDER BY total_products DESC, product_subcategory ASC;

Show the product_id(s), the sum of quantities, where the total sum of its product quantities is greater than or equal to 100.
SELECT product_id, SUM(quantity) as total_quantity FROM product JOIN order_details USING(product_id) GROUP BY product_id HAVING SUM(quantity =>100;
