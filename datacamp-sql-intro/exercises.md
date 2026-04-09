# SQL Exercises

## #1
Get top 10 products with highest rating.

```sql
SELECT id,
       name,
       rating
FROM products
ORDER BY rating DESC
LIMIT 10;
```

Get the top 5 highest value orders.

```sql
SELECT order_id,
       total_amount,
       status
FROM orders
ORDER BY total_amount DESC
LIMIT 5;
```

Get the top 5 lowest value orders.

```sql
SELECT order_id,
       total_amount,
       status
FROM orders
ORDER BY total_amount ASC
LIMIT 5;
```
