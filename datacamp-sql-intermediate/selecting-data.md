# Selecting Data

1. [Setup films database](#1-setup-films-database)
2. [`COUNT()`](#2-count)
3. [`DISTINCT`](#3-distinct)
4. [Combination of `COUNT()` & `DISTINCT`](#4-combination-of-count--distinct)

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
