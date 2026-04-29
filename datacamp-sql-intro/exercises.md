# SQL Exercises

## Example Tables

**products table**  

Here is a products table with sample data.

```sql
CREATE TABLE products (
    id   INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    category     VARCHAR(50)  NOT NULL,
    price        DECIMAL(10, 2) NOT NULL,
    brand        VARCHAR(50)  NOT NULL,
    rating       DECIMAL(2, 1) NOT NULL,
    quantity     INT          NOT NULL
);

INSERT INTO products (id, name, category, price, brand, rating, quantity) VALUES
(1, 'Wireless Bluetooth Headphones', 'Electronics', 89.99,  'TechSound',  4.5, 45),
(2, 'Smartphone Charging Cable',     'Electronics', 12.99,  'PowerLink',  4.2, 200),
(3, 'Laptop Stand Adjustable',       'Electronics', 35.99,  'WorkSpace',  4.7, 67),
(4, 'Wireless Mouse',                'Electronics', 24.99,  'ClickTech',  4.3, 120),
(5, 'USB Flash Drive 32GB',          'Electronics', 15.99,  'DataStore',  4.1, 150),
(6, 'Premium Gaming Keyboard',       'Electronics', 149.99, 'GamePro',    4.8, 0),
(7, 'Smart Watch Fitness Tracker',   'Electronics', 199.99, 'FitTech',    4.6, 23),
(8, 'Noise Canceling Earbuds',       'Electronics', 129.99, 'AudioMax',   4.4, 89);
```

**customers table**  

Here is a customers table with sample data.

```sql
CREATE TABLE customers (
    id          INT PRIMARY KEY,
    name        VARCHAR(50)  NOT NULL,
    family_name VARCHAR(50)  NOT NULL,
    email       VARCHAR(100) NOT NULL UNIQUE,
    city        VARCHAR(50)  NOT NULL,
    signup_date DATE         NOT NULL
);

INSERT INTO customers (id, name, family_name, email, city, signup_date) VALUES
(1, 'Sarah', 'Johnson', 'sarah.johnson@email.com', 'New York',    '2023-01-15'),
(2, 'Mike',  'Chen',    'mike.chen@email.com',     'Los Angeles', '2023-02-20'),
(3, 'Emily', 'Davis',   'emily.davis@email.com',   'Chicago',     '2023-03-10'),
(4, 'James', 'Wilson',  'james.wilson@email.com',  'Houston',     '2023-04-05'),
(5, 'Lisa',  'Brown',   'lisa.brown@email.com',    'Phoenix',     '2023-05-12');
```

---

## What are the top 10 highest rated products in our catalog?

Get top 10 products with highest rating.

```sql
SELECT id,
       name,
       rating
FROM products
ORDER BY rating DESC
LIMIT 10;
```

---

## What are the top 5 highest/lowest value orders?

Get the top 5 highest value orders.

```sql
SELECT id,
       total_amount,
       status
FROM orders
ORDER BY total_amount DESC
LIMIT 5;
```

Get the top 5 lowest value orders.

```sql
SELECT id,
       total_amount,
       status
FROM orders
ORDER BY total_amount ASC
LIMIT 5;
```

---

## What are the unique categories in our product catalog?

```sql
SELECT DISTINCT category AS unique_categories
FROM products;
```

Write a query to find all the unique cities where our customers live. The table is called customers.

```sql
SELECT DISTINCT city
FROM customers;
```
