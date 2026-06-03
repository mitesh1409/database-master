# Filtering Records

## Index

1. [Comparison Operators](#1-comparison-operators)
2. [Multiple Criteria](#2-multiple-criteria)
3. [Filtering Text](#3-filtering-text)
4. [NULL Values](#4-null-values)

---

## #1 Comparison Operators

Comparison operators - `>`, `>=`, `<`, `<=`, `=`, `!=`, `<>`

Examples  

`WHERE` with Numbers

```sql
-- Count the records with at least 100,000 votes
SELECT COUNT(*) AS films_over_100K_votes
FROM reviews
WHERE num_votes >= 100000;
```

`WHERE` with Text

```sql
-- Count the Spanish language films
SELECT COUNT(language) AS count_spanish
FROM films
WHERE language = 'Spanish';
```

---

## #2 Multiple Criteria

We can combine multiple conditions using:  

* `OR` - when you need to satisfy at least one condition
* `AND` - when you need to satisfy all conditions
* `BETWEEN` - filters values withing a specified range - "start" and "end" in range are inclusive

Examples  

```sql
SELECT *
FROM coats
WHERE color = 'Yellow' OR length = 'Short';

SELECT *
FROM coats
WHERE color = 'Yellow' AND length = 'Short';

SELECT *
FROM coats
WHERE buttons BETWEEN 1 AND 5;

SELECT title
FROM films
WHERE release_year = 1994
    OR release_year = 2000;

SELECT title
FROM films
WHERE release_year > 1994
    AND release_year < 2000;

SELECT title
FROM films
WHERE (release_year = 1994 OR release_year = 1995)
    AND (certification = 'PG' OR certification = 'R');

SELECT title
FROM films
WHERE release_year >= 1994
    AND release_year <= 2000;

-- the above query can be re-written using BETWEEN as follows

SELECT title
FROM films
WHERE release_year BETWEEN 1994 AND 2000;
```

---

## #3 Filtering Text

`WHERE` can also filter text data.  

```sql
SELECT title
FROM films
WHERE country = 'Japan';
```

Filter a pattern, rather than specific text.  
`LIKE`, `NOT LIKE`, `IN`

`LIKE` - used to search for a pattern in a field.  
`LIKE` with wildcard `%` - matches zero, one or many characters.  
`LIKE` with wildcard `_` - matches one character.  

```sql
SELECT name
FROM people
WHERE name LIKE 'Ade%';

SELECT name
FROM people
WHERE name LIKE 'Ade_';
```

`NOT LIKE` - used to search the records that don't match the specified pattern.  

```sql
SELECT name
FROM people
WHERE name NOT LIKE 'A%';
```

We can also filter text data based on wildcard character position.  

```sql
-- Find all the names that ends with "r"
SELECT name
FROM people
WHERE name LIKE '%r';

-- Find all the names where the 3rd character is "t"
SELECT name
FROM people
WHERE name LIKE '__t%';
```

NOTE: All of these text operations are case-sensitive.

WHERE, OR - multiple OR conditions  

```sql
SELECT title
FROM films
WHERE release_year = 1920
    OR release_year = 1930
    OR release_year = 1940;
```

We can rewrite the above query using WHERE, IN  

```sql
SELECT title
FROM films
WHERE release_year IN (1920, 1930, 1940);
```

Another example where we are filtering by country.  

```sql
SELECT title
FROM films
WHERE country IN ('Germany', 'France');
```

---

## #4 NULL Values

In SQL, `NULL` represents missing or unknown value.  

Why `NULL` is useful?  

In the real world, our databases will likely have empty fields either because of  
human error or because the information is not available or is unknown.  

How to filter `NULL` values?  

We can use `IS NULL` to filter `NULL` values.  

```sql
-- Get all records where birthdate is missing or is NULL
SELECT name
FROM people
WHERE birthdate IS NULL;

-- Get count of all records where birthdate is missing or is NULL
SELECT COUNT(*) AS no_birthdates
FROM people
WHERE birthdate IS NULL;
```

We can use `IS NOT NULL` to filter non-null values.  

```sql
-- Get all records where birthdate is set or is not null
SELECT name
FROM people
WHERE birthdate IS NOT NULL;

-- Get count of all records where birthdate is set or is not null
SELECT COUNT(*) AS count_birthdates
FROM people
WHERE birthdate IS NOT NULL;
```

`COUNT()` alone vs `COUNT()` with `IS NOT NULL`

```sql
SELECT COUNT(certification) AS count_certification
FROM films;

SELECT COUNT(certification) AS count_certification
FROM films
WHERE certification IS NOT NULL;
```

Both gives the same results.  

NULL put simply  

* NULL values are missing/unknown values
* Very common in the real world data
* Use `IS NULL`/`IS NOT NULL` to:
  - Identify missing values
  - Select missing values
  - Exclude missing values
