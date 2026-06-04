# Aggregate Functions

## Index

1. [Summarizing Data](#1-summarizing-data)
2. [Summarizing Subsets](#2-summarizing-subsets)
3. [`ROUND()`](#3-round())
4. [Aliasing & Arithmetic](#4-aliasing--arithmetic)

---

## #1 Summarizing Data

An aggregate function performs a calculation on several values and returns a single value.  

Some examples of the built-in aggregate functions are:  

* `COUNT()` - Counts the number of rows (ignores NULL values when counting a field/column)
* `AVG()` - Calculates the average of a numeric column
* `SUM()` - Adds up all values in a numeric column
* `MIN()` - Returns the smallest value in a column
* `MAX()` - Returns the largest value in a column

> Key rule: Aggregate functions ignore NULL values — except COUNT(*) which counts all rows including NULLs.

> Aggregate functions operate on the field (column) not the records (rows).

Examples  

```sql
SELECT AVG(budget)
FROM films;

SELECT SUM(budget)
FROM films;

SELECT MIN(budget)
FROM films;

SELECT MAX(budget)
FROM films;
```

`AVG()` & `SUM()` can be used on numerical fields only.  

`COUNT()`, `MIN()` & `MAX()` can be used on numerical as well as non-numerical fields.  

```sql
SELECT MIN(country)
FROM films;

SELECT MAX(country)
FROM films;
```

It is a best practice to use an alias when summarizing data so that our results  
are clear to anyone reading our code.

---

## #2 Summarizing Subsets

```sql
-- Get the average budget of films released on or after 2010.
SELECT AVG(budget) AS avg_budget
FROM films
WHERE release_year >= 2010;

SELECT SUM(budget) AS sum_budget
FROM films
WHERE release_year = 2010;

SELECT MIN(budget) AS min_budget
FROM films
WHERE release_year = 2010;

SELECT MAX(budget) AS max_budget
FROM films
WHERE release_year = 2010;

SELECT COUNT(budget) AS count_budget
FROM films
WHERE release_year = 2010;
```

---

## #3 `ROUND()`

`ROUND()` - round a number to a specified decimal.

It can be used with numerical fields only.

It takes two arguments - number to round and decimal places.

When you skip the second parameter it will round to a whole number.

```sql
-- Average budget rounded to 2 decimal places.
SELECT ROUND(AVG(budget), 2) AS avg_budget
FROM films
WHERE release_year >= 2010;

-- Average budget rounded to a whole number.
SELECT ROUND(AVG(budget)) AS avg_budget
FROM films
WHERE release_year >= 2010;
```

`ROUND()` takes two parameters: `ROUND(number, decimal_places)`.

When you pass a **negative** value as the second parameter, it rounds to the **left** of the decimal point — i.e., it rounds the integer part.

```sql
SELECT ROUND(1234.567,  2)   -- →  1234.57   (2 decimal places)
SELECT ROUND(1234.567,  0)   -- →  1235      (nearest whole number)
SELECT ROUND(1234.567, -1)   -- →  1230      (nearest 10)
SELECT ROUND(1234.567, -2)   -- →  1200      (nearest 100)
SELECT ROUND(1234.567, -3)   -- →  1000      (nearest 1000)
SELECT ROUND(1234.567, -4)   -- →  0         (nearest 10000)
```

Think of it as a number line:

```
← negative side          positive side →

-3    -2    -1    0    1    2    3
1000  100   10    1   0.1  0.01  0.001
```

**Practical use case:** useful when working with large numbers where exact precision doesn't matter — e.g., rounding revenue figures to the nearest thousand for a summary report.

---

## #4 Aliasing & Arithmetic

**Arithmetic Operations**  

`+`, `-`, `*`, `/`

```sql
SELECT (2 + 3); -- 5
SELECT (4 - 3); -- 1
SELECT (4 * 3); -- 12
SELECT (12 / 3); -- 4
SELECT (4 / 3); -- 1 - it returns an integer result
SELECT (4.0 / 3.0); -- 1.3333... - it returns a float result
```

**Aggregate Functions Vs Arithmetic Operations**  

Aggregate Functions operate on the fields only - vertically.

Arithmetic Operations can operate on rows - horizontally.

```sql
-- Find out how much a movie earned
SELECT (gross - budget) AS profit
FROM films;
```

> We will always need to use an alias when summarizing data with aggregate functions and arithmetic operations.

> Aliases defined in the `SELECT` clause cannot be used in the `WHERE` clause due to order of execution - `WHERE` executes before `SELECT`.

Examples  

```sql
-- duration is in mins in films table.
-- Calculate the title and duration_hours from films
SELECT title, (duration / 60.0) AS duration_hours
FROM films;

-- Calculate the percentage of people who are no longer alive
SELECT (COUNT(deathdate) * 100.0 / COUNT(*)) AS percentage_dead
FROM people;
```
