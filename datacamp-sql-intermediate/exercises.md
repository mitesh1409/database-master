# SQL Exercises

## #1 Create films database along with tables - films, people, reviews & roles

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

## #2 
