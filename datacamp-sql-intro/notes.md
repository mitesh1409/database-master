# Introduction to SQL

**Index**  

1. Databases
2. Data Organization
3. SQL Basics
4. Column Selection
5. Sorting
6. Row Limiting
7. Temporary Results OR Query Results
8. Error Handling
9. Finding Unique Values
10. Column Aliasing
11. SQL Style Conventions
12. Different Database Systems OR SQL Flavors
13. Database Server & Database Client, Client-Server Architecture
14. SQL Client OR Database Client
15. Database Access
16. Materialized Views

---

## #1 Databases

* A database is a system for managing data that allows us to store data reliably, retrieve information efficiently, and manipulate data systematically.

* Databases power web, mobile and other applications, as well as data analysis and reporting.

---

## #2 Data Organization

* Data in a database is organized into tables with rows and columns.

* Rows represent individual entities or events.

* Columns represent specific attributes or properties.

---

## #3 SQL Basics

* Structured Query Language (SQL) is the most widely used programming language for working with data in databases.

* In its most common usage, you write SQL code to request data from databases, and they return the results you need.

* SQL is much simpler than general programming languages because it's designed for one specific purpose: working with data.

---

## #4 Column Selection

```sql
SELECT column_1,
       column_2
FROM table_1;
```

* We use `SELECT` to specify which columns to retrieve and `FROM` to specify the table to retrieve from.

* `SELECT column_1`: selects one column

* `SELECT column_1, column_2`: selects multiple columns

* `SELECT *`: selects all columns

---

## #5 Sorting

```sql
SELECT column_1,
       column_2
FROM table_1
ORDER BY column_1 DESC;
```

* We use `ORDER BY` to sort rows by a specific column.

* `ORDER BY column_1`: By default it sorts in Ascending order (lowest to highest A-Z, 0-9). You can also use `ASC` keyword to clearly specify the Ascending order.

* `ORDER BY column_1 DESC`: Use `DESC` keyword to sort in Descending order (highest to lowest Z-A, 9-0).

---

## #6 Row Limiting

```sql
SELECT *
FROM table_1
LIMIT 10;
```

* We use `LIMIT` to restrict the number of rows returned by a query.

* `LIMIT` is placed at the end of the query and returns the first N rows produced by the query.

* **Limiting is an essential practice because it prevents slow, expensive queries** and protects against mistakes that might return far more data than expected.

**Limiting results is essential practice in SQL**  

If a query returns many rows, it could take a long time to run and cost significant  
money (many cloud databases charge by data volume).  

Even when you expect a small result set, limiting protects against mistakes that  
might return far more data than expected.

---

## #7 Temporary Results OR Query Results

* The original database remains unchanged when you run `SELECT` queries.

* Query results are **temporary snapshots** of the data. To keep results for later use, you must save them to your computer or cloud storage.

The result of a query is a temporary copy of the data — it's not stored in the database.

You need to intentionally save it to your computer (or the cloud) if you want to keep it for later use.

**Saving Query Results**  

There are several ways to save query results in practice:

#1 Export to files  
Most SQL clients let you export results to CSV, Excel, or other formats for sharing or analysis. This is the simplest approach for one-time results.

#2 Create tables  
You can save results as a new permanent table in the database using `CREATE TABLE AS SELECT` or `INSERT INTO`. This is common for staging data or creating reporting tables.

#3 Views  
Views are virtual tables that store the query definition, not the actual data. When you query a view, it runs the underlying query fresh each time. Views are great for simplifying complex queries or controlling access, but they don't avoid the expense of re-running the query.

#4 Materialized views  
These are pre-computed results stored as actual data. They're refreshed periodically rather than computed on every query. They are widely used in industry for expensive queries like aggregations and reports. The tradeoff is keeping them updated.

In case of expensive queries, materialized views are indeed the industry-standard solution!

---

## #8 Error Handling

* **SQL requires precise syntax**, and small mistakes can prevent queries from running.

* Read error messages carefully, check spelling, use a good development environment, and leverage AI assistance.

* The more you work with SQL, the fewer mistakes you'll make and the faster you'll spot and fix them.

---

## #9 Finding Unique Values

```sql
SELECT DISTINCT column_name
FROM table_name;
```

* We use `DISTINCT` to retrieve only unique values from a column, eliminating duplicates.

* `DISTINCT` is placed immediately after `SELECT`.

---

## #10 Column Aliasing

```sql
SELECT column_1 AS alias_name,
       column_2
FROM table_1;
```

* The `AS` keyword assigns temporary names to columns in query results to improve readability.

* The alias appears in column headers but doesn't change the original database column name.

* Effective aliases should be descriptive, consistent, and use snake_case convention for best practices.

Guidelines for creating aliases:  

1. Descriptive: Clearly indicate what the data represents (like unique_categories instead of just cat)

2. Valid: Use only letters, numbers, and underscores

3. Readable: Use lowercase with underscores between words—this is called snake_case convention (like customer_cities or total_sales)

---

## #11 SQL Style Conventions

You can write SQL keywords in uppercase or lowercase — both work fine.  
However, the convention is to use uppercase (like SELECT, FROM, DISTINCT) because  
it makes it easier to distinguish SQL keywords from table and column names.

This makes your code much easier to read and understand,  
especially as your queries get more complex.

---

## #12 Different Database Systems OR SQL Flavors

Different database systems exist, and some of the most popular ones are  
PostgreSQL, SQL Server, MySQL, and BigQuery. Each system has its own flavor  
of SQL with minor differences in syntax.

However, the fundamental concepts and most common SQL commands are the same across flavors.  

Here are a couple of examples of the differences:  

**#1 Limiting results:**

* In PostgreSQL, MySQL, and BigQuery, we use: `LIMIT 10`
* In SQL Server, we use: `SELECT TOP(10)`

**#2 Case sensitivity:**

* BigQuery is case-sensitive — so `customer`, `Customer`, and `CUSTOMER` are different column names
* PostgreSQL, SQL Server, and MySQL are not case-sensitive for column names, table names, and keywords—so `customer`, `Customer`, and `CUSTOMER` refer to the same column

The good news? Once you master the core SQL concepts, adapting to different flavors is straightforward.

---

## #13 Database Server & Database Client, Client-Server Architecture

How the connection between you and the database actually works?  

There are two systems involved:  

1. **Database Server**: The computer that stores and runs the database. It processes your queries and returns results.

2. **Database Client**: The software that runs on your computer. It lets you connect to a database, type your SQL queries, send them to the server to run, and see the results.

Multiple clients can connect to the same database server and run queries simultaneously. Each sees the same data (unless permissions differ).

This is called a **client-server architecture**.

---

## #14 SQL Client OR Database Client

To write and run SQL queries, you need a SQL client.  

SQL clients come in different forms:  

1. **Web Applications**: Browser-based tools provided by cloud databases or platforms. Examples include **BigQuery Console** and **Snowflake Web UI**. Easy to start — nothing to install.

2. **Desktop Applications**: Software you install on your computer with a friendly interface for writing and running queries. Examples include **DBeaver**, **TablePlus**, and **DataGrip**.

The great news? The SQL you write is the same regardless of which client you use!

---

## #15 Database Access

To connect to a traditional database, you need to provide:

* Server address: Where the database lives (URL or IP address)
* Username: Your account name
* Password: Your account password
* Database name: Which specific database to use

When working at an organization, you'll need to obtain these connection details from your  
database administrator.

For cloud databases like BigQuery or Snowflake, the process is similar — you'll need to  
request access permissions and credentials from your IT team or admin.

---

## #16 Materialized Views

* These are pre-computed results stored as actual data.  

* They're refreshed periodically rather than computed on every query.  

* Widely used in industry for expensive queries like aggregations and reports. The tradeoff is keeping them updated/fresh.

For expensive queries, materialized views are indeed the industry-standard solution!

Materialized Views are kind of the *opposite* of Temporary Results.

Here's the distinction:

- **Temporary Results** are snapshots that exist only for the duration of a query session. Once you close the connection or session, they're gone.
- **Materialized Views** are *persistently stored*, pre-computed results saved in the database itself. They survive sessions and are refreshed on a schedule.

So a Materialized View is not used to store Temporary Results — it's used to avoid recomputing *expensive* queries repeatedly by storing the output long-term.

**Real-life example — E-commerce Monthly Sales Report**  

Imagine an e-commerce platform like Flipkart with millions of orders. Every time the business team opens their dashboard, they want to see *total sales by product category for the current month*.

Without a materialized view, the database would have to scan millions of order rows and aggregate them on every single dashboard load — extremely slow and expensive.

With a materialized view, you pre-compute something like:

```sql
CREATE MATERIALIZED VIEW monthly_sales_summary AS
SELECT category, SUM(amount) AS total_sales, COUNT(*) AS order_count
FROM orders
WHERE order_date >= DATE_TRUNC('month', CURRENT_DATE)
GROUP BY category;
```

Now the dashboard just reads from `monthly_sales_summary` instantly. The view is refreshed once a day (or every few hours), so the data stays reasonably fresh without hammering the database on every query.

**The key takeaway:** Materialized views trade *storage space + refresh overhead* for *query speed*. Temporary results trade *permanence* for *simplicity*. They solve different problems.
