# Selecting Data

1. [Setup films database](#1-setup-films-database)
2. [`COUNT()`](#2-count)
3. [`DISTINCT`](#3-distinct)
4. [Combination of `COUNT()` & `DISTINCT`](#4-combination-of-count--distinct)
5. [Query Execution](#5-query-execution)
6. [Debugging SQL](#6-debugging-sql)
7. [SQL Style](#7-sql-style)

---

## #1 Setup films database

Create films database along with tables - films, people, reviews & roles.

```sql
-- Create films database
CREATE DATABASE films_database;

-- 1. Create 'films' table (Parent table)
CREATE TABLE films (
    id INT4 PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    release_year INT4,
    country VARCHAR(100),
    duration INT4,
    language VARCHAR(100),
    certification VARCHAR(50),
    gross INT8,
    budget INT8
);

-- 2. Create 'people' table (Parent table)
CREATE TABLE people (
    id INT4 PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    birthdate DATE,
    deathdate DATE
);

-- 3. Create 'reviews' table (Child table linked to films)
CREATE TABLE reviews (
    id INT4 PRIMARY KEY,
    film_id INT4,
    num_user INT4,
    num_critic INT4,
    imdb_score FLOAT4,
    num_votes INT4,
    facebook_likes INT4,
    FOREIGN KEY (film_id) REFERENCES films(id) ON DELETE CASCADE
);

-- 4. Create 'roles' table (Junction table linking films and people)
CREATE TABLE roles (
    id INT4 PRIMARY KEY,
    film_id INT4,
    person_id INT4,
    role VARCHAR(255),
    FOREIGN KEY (film_id) REFERENCES films(id) ON DELETE CASCADE,
    FOREIGN KEY (person_id) REFERENCES people(id) ON DELETE CASCADE
);
```

---

## #2 `COUNT()`

It counts the number of records with a value in a field.

`COUNT(field_name)`  
Counts non-null values in a field. It ignores NULL values.

`COUNT(*)`  
This counts every single row that matches your query criteria, completely ignoring whether individual columns contain NULL values or not.

**Examples**  

```sql
-- Count total records, ignores NULL values in one or multiple columns.
SELECT COUNT(*) AS total_records
FROM people;

-- Count non-null birthdate values
SELECT COUNT(birthdate) AS count_birthdates
FROM people;

-- Count non-null birthdate and name values
SELECT COUNT(birthdate) AS count_birthdates, COUNT(name) AS count_names
FROM people;
```

---

## #3 `DISTINCT`

It removes duplicates to return only unique values.  
Also it treats NULL as a unique value.  

**Examples**  

```sql
-- Following returns all the language values including NULL
SELECT language
FROM films;

-- Following returns unique language values including NULL
SELECT DISTINCT language
FROM films;
```

---

## #4 Combination of `COUNT()` & `DISTINCT`

Combine `COUNT()` with `DISTINCT` to count unique non-null values.  
`COUNT()` ignores NULL values, so combination of `COUNT()` & `DISTINCT` gives count of  
unique non-null values.

```sql
-- Following returns the count of unique non-null birthdate values.
SELECT COUNT(DISTINCT birthdate) AS count_distinct_birthdates
FROM people;
```

---

## #5 Query Execution

Unlike many programming languages, SQL code is not processed in the order it is written.  

```
┌─────────────────────────────────────────┐
│         SQL ORDER OF EXECUTION          │
└─────────────────────────────────────────┘

        ┌─────────────────────────┐
   ①    │      FROM / JOIN        │  ← Load tables, apply joins
        └────────────┬────────────┘
                     │
        ┌────────────▼────────────┐
   ②    │         WHERE           │  ← Filter rows (before grouping)
        └────────────┬────────────┘
                     │
        ┌────────────▼────────────┐
   ③    │        GROUP BY         │  ← Group rows by column(s)
        └────────────┬────────────┘
                     │
        ┌────────────▼────────────┐
   ④    │         HAVING          │  ← Filter groups (after grouping)
        └────────────┬────────────┘
                     │
        ┌────────────▼────────────┐
   ⑤    │         SELECT          │  ← Pick columns, run expressions
        └────────────┬────────────┘
                     │
        ┌────────────▼────────────┐
   ⑥    │        DISTINCT         │  ← Remove duplicate rows
        └────────────┬────────────┘
                     │
        ┌────────────▼────────────┐
   ⑦    │        ORDER BY         │  ← Sort the result set
        └────────────┬────────────┘
                     │
        ┌────────────▼────────────┐
   ⑧    │      LIMIT / OFFSET     │  ← Trim to final row count
        └─────────────────────────┘
```

The key insight: **`SELECT` is step 5**, not step 1 — even though you write it first. This is why you can't use a `SELECT` alias in a `WHERE` clause (that alias doesn't exist yet when `WHERE` runs).

A few practical gotchas this explains:

- **`WHERE` vs `HAVING`** — `WHERE` filters *rows* before grouping; `HAVING` filters *groups* after. Use `WHERE` whenever you can — it's faster.
- **Can't use aliases in `WHERE`** — the alias is defined in `SELECT` (step 5), which runs after `WHERE` (step 2).
- **`ORDER BY` can use aliases** — it runs after `SELECT`, so the alias is already defined by then.
- **`LIMIT` is last** — sorting happens first, then the rows are trimmed. This matters for correctness.

---

## #6 Debugging SQL

### Key Points for Debugging SQL

**1. Read the error message**  

SQL errors usually tell you the **line number** and **what went wrong**. Read it before guessing.

**2. Common mistakes**  

| Mistake | Example |
|---|---|
| Misspelling | `SELCT` instead of `SELECT` |
| Wrong capitalization | Usually fine — SQL keywords are case-insensitive, but **string values are not**: `'Alice' ≠ 'alice'` |
| Missing comma | Between column names in `SELECT` |
| Extra comma | Trailing comma before `FROM` |
| Wrong quotes | Using `"double"` for strings instead of `'single'` (varies by DB) |
| Missing alias | Referencing a `SELECT` alias in `WHERE` (not allowed — see execution order) |

**3. Debugging strategy**  

1. **Run a simpler version first** — strip it down to `SELECT * FROM table` and add clauses back one by one.
2. **Check the execution order** — if a clause isn't working, ask *"does that column/alias even exist at this step?"*
3. **Isolate subqueries** — run inner queries independently to verify their output.
4. **Check your data** — sometimes the query is correct but the data isn't what you expect. Use `SELECT *` to inspect.

**Quick checklist before running**  

✓ All keywords spelled correctly?  
✓ Commas between every column in SELECT?  
✓ Matching parentheses ( )?  
✓ String values in single quotes ' '?  
✓ Table and column names correct (case-sensitive in some DBs)?  
✓ JOIN has an ON condition?  

---

## #7 SQL Style

**SQL Formatting**  

* Formatting is not required
* But lack of formatting can cause issues - query becomes hard to read and understand as it grows

BAD Formatting Example  

```sql
select title, release_year, country from films limit 3;
```

Above query works fine but it is not easy to read and understand.  

GOOD Formatting Example  

```sql
SELECT title, release_year, country
FROM films
LIMIT 3;
```

Reference:  
[SQL style guide by Simon Holywell](https://www.sqlstyle.guide/)

**Non-standard fields**  

Example  

Here "facebook likes" is a non-standard field.  

```sql
-- Create 'reviews' table (Child table linked to films)
CREATE TABLE reviews (
    id INT4 PRIMARY KEY,
    film_id INT4,
    num_user INT4,
    num_critic INT4,
    imdb_score FLOAT4,
    num_votes INT4,
    "facebook likes" INT4,
    FOREIGN KEY (film_id) REFERENCES films(id) ON DELETE CASCADE
);

-- Selects film_id and "facebook likes" from all the reviews.
SELECT film_id, "facebook likes"
FROM reviews;
```

Using **underscores** (`facebook_likes`) is the standard SQL naming convention — it's cleaner, more portable, and avoids the need for quotes entirely.

**Why avoid quoted/non-standard names?**  

| Issue | `"facebook likes"` | `facebook_likes` |
|---|---|---|
| Requires quotes everywhere | ✅ Yes, always | ❌ No |
| Portable across databases | ❌ Not always | ✅ Yes |
| Risk of typo errors | Higher | Lower |
| Readable & conventional | ❌ No | ✅ Yes |

Stick to **lowercase + underscores** and you'll rarely need to quote anything.

### Key Rules from Simon Holywell's SQL Style Guide

### General

```
✓ Use UPPERCASE for reserved keywords  →  SELECT, WHERE, FROM
✓ Use lowercase for column and table names
✓ Use underscores for spaces            →  first_name, not firstName
✓ Use single quotes for strings         →  WHERE name = 'Alice'
✓ Store dates in ISO 8601 format        →  YYYY-MM-DDTHH:MM:SS
✓ Use standard SQL functions over vendor-specific ones
```

### Naming

| Thing | Rule | Example |
|---|---|---|
| Tables | Collective/singular noun, no prefix | `staff`, not `tbl_employees` |
| Columns | Singular, lowercase | `first_name`, not `FirstNames` |
| Aliases | Use `AS`, initials of the object | `FROM staff AS s` |
| Keys | Prefer `_id`, `_num`, `_date` suffixes | `film_id`, `created_date` |

Avoid: `camelCase`, `tbl_` prefixes, quoted identifiers, plurals.

### Formatting — The "River" Style

Right-align keywords and left-align values to create a visual gap down the middle — makes queries easy to scan:

```sql
SELECT a.title,
       a.release_date
  FROM albums AS a
 WHERE a.title = 'Charcoal Lane'
    OR a.title = 'The New Danger';
```

Notice `SELECT`, `FROM`, `WHERE` all end at the same column.

### Query Best Practices

```
✓ Use BETWEEN instead of multiple ANDs
✓ Use IN() instead of multiple ORs
✓ Use CASE for conditional logic
✗ Avoid UNION and temp tables where possible
✗ Avoid SELECT * in production queries
```

### `CREATE TABLE` Style

```sql
CREATE TABLE staff (
    PRIMARY KEY (staff_num),
    staff_num   INT(5)       NOT NULL,
    first_name  VARCHAR(100) NOT NULL,
    last_name   VARCHAR(100) NOT NULL
);
```

- Primary key first
- Align column definitions with consistent spacing
- Indent by 4 spaces inside `CREATE`

### Comments

```sql
-- Single line comment

/* Multi-line comment
   for longer explanations */
```
