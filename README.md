# Java- Databases: Group By & Having

1. Select all orders that have total price greater than 300.
```sql
SELECT
  o.order_id,
  o.customer_id,
  ROUND(SUM(od.unit_price * od.quantity) :: NUMERIC, 2) AS total_price 
FROM
  orders o 
LEFT JOIN
  order_details od ON o.order_id = od.order_id
GROUP BY
  o.order_id, o.customer_id, od.unit_price, od.quantity
HAVING
  (od.unit_price * od.quantity) > 300
ORDER BY
  total_price;
```
2. Select only employees that have more than 100 orders.
```sql
SELECT
  e.employee_id,
  first_name,
  last_name,
  COUNT(o.order_id) AS total_orders
FROM
  employees e
FULL JOIN
  orders o ON e.employee_id = o.employee_id
GROUP BY
  e.employee_id, first_name, last_name, order_id
HAVING
  COUNT(order_id) > 100
```
3. Select the latest order (order_id, customer_id, order_date) for each customer. Include only orders that were made after 1998-05-05.
```sql
SELECT
  order_id, customer_id,
  MAX(order_date) AS latest_order_date 
FROM
  orders o
GROUP BY
  order_id, customer_id
HAVING
  MAX(order_date) > '1998-05-05'
```
4. Select shipper company name and calculate how many orders have each.
```sql
SELECT
  company_name,
  COUNT(ship_via) AS total_orders
FROM
  shippers s
LEFT JOIN
  orders o ON s.shipper_id = o.ship_via
GROUP BY
  company_name
```
5. Select the most expensive order.
```sql
  SELECT
  order_id,
  ROUND(SUM(unit_price * quantity)::NUMERIC,2) AS total_price 
FROM
  order_details od
GROUP BY
  order_id
ORDER BY
  total_price DESC
LIMIT 1
```
6. Select an order that have the most items in it.
```sql
SELECT
  order_id ,
  COUNT(product_id) AS total_items 
FROM
  order_details 
GROUP BY
  order_id 
ORDER BY
  total_items DESC 
LIMIT 1
```
7. Select orders that was shipped by company with id = 1 and contains more than 4 items.
```sql
SELECT
  o.order_id,
  o.ship_via,
  COUNT(od.product_id) AS total_items 
FROM
  orders o 
LEFT JOIN
  order_details od ON o.order_id = od.order_id
GROUP BY
  o.order_id, o.ship_via
HAVING 
  o.ship_via = 1 
AND
  COUNT(od.product_id) > 4;
```
8. Select category id, category name and sumarize quantity of products for each category. Take into consideratation only categories with id: 1,2,3 and list only those where sum of products is bigger than 6000.
```sql
SELECT
  c.category_id,
  c.category_name,
  SUM(p.units_in_stock) AS total_items
FROM
  categories c
LEFT JOIN
  products p ON c.category_id = p.category_id
GROUP BY
  c.category_id, c.category_name
HAVING
  c.category_id IN (1,2,3)
AND
  SUM(p.units_in_stock) > 500
```
9. Make a report on the 10 best selling products with categories in 1997.
```sql
SELECT
  p.product_id,
  p.product_name,
  c.category_name,
  SUM(od.quantity) AS total_quantity 
FROM
  products p
LEFT JOIN
  categories c ON p.category_id = c.category_id
LEFT JOIN
  order_details od ON p.product_id = od.product_id
LEFT JOIN
  orders o ON o.order_id = od.order_id
WHERE
  EXTRACT(YEAR FROM o.order_date) = 1997
GROUP BY
  p.product_id, p.product_name, c.category_name
ORDER BY
  total_quantity DESC
LIMIT 10
```
10. Select the least popular categories of products for orders made in 1997.
```sql
SELECT
  p.product_id,
  p.product_name,
  c.category_name,
  SUM(od.quantity) AS total_quantity 
FROM
  products p
LEFT JOIN
  categories c ON p.category_id = c.category_id
LEFT JOIN
  order_details od ON p.product_id = od.product_id
LEFT JOIN
  orders o ON o.order_id = od.order_id
WHERE
  EXTRACT(YEAR FROM o.order_date) = 1997
GROUP BY
  p.product_id, p.product_name, c.category_name
ORDER BY
  total_quantity ASC
LIMIT 1
```
