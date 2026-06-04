# Sorting & Grouping

## Index

1. [Sorting Results](#1-sorting-results)
2. [Grouping Data](#2-grouping-data)
3. [Filtering Grouped Data](#3-filtering-grouped-data)

---

## #1 Sorting Results

In SQL, `ORDER BY` keyword is used to sort results of one or more fields.

`ORDER BY` will sort in ascending order by default.

```sql
-- Get the films sorted by budget (ASC by default).
SELECT title, budget
FROM films
ORDER BY budget;

-- Get the films sorted by title (ASC by default).
SELECT title, budget
FROM films
ORDER BY title;

-- Sorted by budget - ASC, lowest to highest
SELECT title, budget
FROM films
ORDER BY budget ASC;

-- Sorted by budget - DESC, highest to lowest
SELECT title, budget
FROM films
ORDER BY budget DESC;

SELECT title, budget
FROM films
WHERE budget IS NOT NULL
ORDER BY budget DESC;
```

Notice that we don't have to select the field we are sorting on.  
For example, here's a query where we sort by release year and only look at the title.  

However, it is a good idea to include the field we are sorting on in the SELECT statement for clarity.

```sql
SELECT title
FROM films
ORDER BY release_year;

SELECT title, release_year
FROM films
ORDER BY release_year;
```

`ORDER BY` can also be used to sort on multiple fields.  
It will sort by the first field specified, then sort by the next, etc.  
To specify multiple fields, we seperate the field names with a comma.  

`ORDER BY field_one, field_two...`

```sql
SELECT title, wins
FROM best_movies
ORDER BY wins DESC;

SELECT title, wins, imdb_score
FROM best_movies
ORDER BY wins DESC, imdb_score DESC;
```

---

## #2 Grouping Data

`GROUP BY` - allows us to group by single or multiple fields.

### `GROUP BY` single field

In the real world, we'll often need to summarize data for a particular group of results. For example, we might want to see the film data grouped by certification and make calculations on those groups, such as the average duration for each certification.

```sql
SELECT certification, COUNT(title) AS title_count
FROM films
GROUP BY certification;
```

### Error Handling

SQL will return an error if we try to SELECT a field that is not in our GROUP BY clause. We'll need to correct this by adding an aggregate function around title.

```sql
-- Following gives an error
SELECT certification, title
FROM films
GROUP BY certification;

-- Following works
SELECT certification, COUNT(title) AS title_count
FROM films
GROUP BY certification;
```

### `GROUP BY` multiple fields

```sql
SELECT certification, language, COUNT(title) AS title_count
FROM films
GROUP BY certification, language;
```

When grouping by multiple fields, SQL creates a **unique group for every combination** of the fields.

So for `GROUP BY certification, language` — it groups by `certification` first, then splits each certification further by `language`.

**Sample Data (`films` table)**  

| title | certification | language |
|---|---|---|
| Film A | PG | English |
| Film B | PG | English |
| Film C | PG | French |
| Film D | R | English |
| Film E | R | Spanish |
| Film F | R | Spanish |

**Query Output**  

| certification | language | title_count |
|---|---|---|
| PG | English | 2 |
| PG | French | 1 |
| R | English | 1 |
| R | Spanish | 2 |

**How to read this**  

```
PG  ──┬── English  → 2 films   (Film A, Film B)
      └── French   → 1 film    (Film C)

R   ──┬── English  → 1 film    (Film D)
      └── Spanish  → 2 films   (Film E, Film F)
```

Each **unique pair** of `(certification, language)` becomes its own group with its own `COUNT()`. So `PG + English` and `PG + French` are treated as **two separate groups**, not merged under `PG`.

### `GROUP BY` with `ORDER BY`

```sql
SELECT certification, COUNT(title) AS title_count
FROM films
GROUP BY certification;

SELECT certification, COUNT(title) AS title_count
FROM films
GROUP BY certification
ORDER BY title_count DESC;
```

### Examples

```sql
-- Find the release_year and film_count of each year
SELECT release_year, COUNT(*) AS film_count
FROM films
GROUP BY release_year;

-- Find the release_year and average duration of films for each year
SELECT release_year, AVG(duration) AS avg_duration
FROM films
GROUP BY release_year;

-- Find the release_year, country, and max_budget, then group and order by release_year and country
SELECT release_year, country, MAX(budget) AS max_budget
FROM films
WHERE budget IS NOT NULL
GROUP BY release_year, country
ORDER BY release_year, country;
```

In the real world, every SQL query starts with a business question. Then it is up to you to decide how to write the query that answers the question. Let's try this out.

Using the films table: which release_year had the most language diversity?

```sql
SELECT release_year, COUNT(DISTINCT language) AS language_count
FROM films
WHERE language IS NOT NULL AND release_year IS NOT NULL
GROUP BY release_year
ORDER BY language_count DESC
LIMIT 1;
```

---

## #3 Filtering Grouped Data

In SQL, we cannot filter aggregate functions with `WHERE` clauses.

We need to use `HAVING` clause instead of `WHERE` clause to filter grouped data.  

```sql
-- Following does not work - can't filter groups using WHERE
SELECT release_year, COUNT(title) AS title_count
FROM films
GROUP BY release_year
WHERE COUNT(title) > 10;

-- Following works - can filter groups using HAVING
SELECT release_year, COUNT(title) AS title_count
FROM films
GROUP BY release_year
HAVING COUNT(title) > 10;
```

```sql
SELECT
    certification,
    COUNT(title) AS title_count
FROM films
WHERE certification IN ('G', 'PG', 'PG-13')
GROUP BY certification
HAVING COUNT(title) > 500
ORDER BY title_count DESC
LIMIT 3;
```

**`HAVING` vs `WHERE`**  

| Data | How to filter? |
| --- | --- |
| Non-grouped Data | `WHERE` |
| Grouped Data | `HAVING` |

`WHERE` filters individual records, `HAVING` filters grouped records.  

**Examples**  

What films were released in the year 2000?

```sql
SELECT title AS films_released_in_2000
FROM films
WHERE release_year = 2000;
```

In what years was the average film duration over two hours?

```sql
SELECT release_year, AVG(duration) AS avg_duration
FROM films
GROUP BY release_year
HAVING AVG(duration) > 120;
```

Practice using `HAVING` to find out which countries (or country) have the most varied film certifications.  
Where "the most varied film certification" is defined as more than 10 certifications.

```sql
SELECT country, COUNT(DISTINCT certification) AS certification_count
FROM films
WHERE country IS NOT NULL
    AND certification IS NOT NULL
GROUP BY country
HAVING COUNT(DISTINCT certification) > 10;
```

Write a query showing what countries have the highest average film budgets.
Where "the highest average film budget" is defined as countries with an average budget of more than one billion (1000000000).

```sql
SELECT country, ROUND(AVG(budget), 2) AS average_budget
FROM films
GROUP BY country
HAVING ROUND(AVG(budget), 2) > 1000000000
ORDER BY average_budget DESC;
```

Write a query that returns the average budget and average gross earnings for films each year after 1990 if the average budget is greater than 60 million.

```sql
SELECT
    release_year,
    ROUND(AVG(budget), 2) AS avg_budget,
    ROUND(AVG(gross), 2) AS avg_gross
FROM films
WHERE
    release_year > 1990
GROUP BY release_year
HAVING AVG(budget) > 60000000
ORDER BY avg_gross DESC
LIMIT 1;
```
