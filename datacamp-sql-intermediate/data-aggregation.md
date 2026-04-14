# Intermediate SQL - Data Aggregation

**Index**  

1. Data Aggregation
2. Functions
3. Summarizing Data in SQL
4. Summary Operations
5. Design First, Then Implement
6. How Data is Organized
7. Grouped Aggregation
8. Grouped Aggregation Deep Dive - Split-Apply-Combine
9. Order of SQL Clauses
10. Grouping by Multiple Columns
11. Metric Reliability
12. Trade-offs of Many Grouping Columns
13. Handling Missing Groups
14. Multi-Column Sorting

---

## #1 Data Aggregation

Data aggregation is your first data manipulation operation — it's the process of summarizing data to obtain meaningful insights. Even with moderate datasets, patterns are hard to spot in raw data. Summarizing allows you to wrap your mind around it and draw conclusions.

For a Data Scientist, this is crucial. Whether you're extracting features for ML models, analyzing patterns in user behavior, or validating data quality, aggregation helps you quickly understand distributions, identify outliers, and generate summary statistics that inform your modeling decisions.

* Data aggregation is the operation of summarizing data by applying functions (like `SUM`, `AVG`, `COUNT`) to columns to produce single values.

* Data aggregation reveals insights and patterns that are difficult to recognize by examining raw data alone.

---

## #2 Functions

Example: Get total revenue (sum of the amounts of all the orders) of all times from the orders table.

```sql
SELECT SUM(amount) AS `Total Revenue`
FROM orders;
```

In SQL, operations on data are performed using **functions**. Functions are one of the most important programming concepts in data manipulation.

Functions have three key components:

* **Name**: Identifies the operation, such as `SUM`

* **Inputs**: The data to operate on, placed inside `()` after the function name. For example, `SUM(amount)` operates on the `amount` column

* **Output**: The result after performing the operation. `SUM(amount)` returns the total of all values

Note: In programming, `()` are called **parentheses**, not brackets. Function names are written with parentheses in documentation (like `SUM()`) to distinguish them from table or column names (like `orders` or `amount`).

---

## #3 Summarizing Data in SQL

* To apply summary operations in SQL, use aggregate functions within a SELECT statement:  

```sql
SELECT COUNT(column_1) AS metric_1,
       SUM(column_2)   AS metric_2
FROM table_name;
```

* The AS keyword assigns a temporary name (alias) to a column, making output more readable.

* Calculate multiple summary values in one query by listing aggregate functions separated by commas.

```sql
SELECT COUNT(order_id)         AS order_count,
       COUNT(DISTINCT user_id) AS user_count,
       SUM(amount)             AS revenue,
       AVG(amount)             AS avg_amount
FROM orders;

SELECT COUNT(trip_id) AS total_trips,
       COUNT(DISTINCT rider_id) AS total_riders,
       COUNT(DISTINCT driver_id) AS total_drivers,
       SUM(fare) AS total_fare,
       AVG(fare) AS average_fare
FROM trips;
```

* You can also calculate just one summary value by using a single aggregate function.

---

## #4 Summary Operations

* SUM(): Calculates the total of all values in a column

* AVG(): Calculates the average value in a column

* MIN(): Finds the minimum value in a column

* MAX(): Finds the maximum value in a column

* COUNT(): Counts the number of rows or values

* COUNT(DISTINCT column): Counts the number of unique values

---

## #5 Design First, Then Implement

**Thinking through your query before writing code** is one of the most effective habits of successful data analysts.

When designing a data aggregation operation, answer two key questions:

1. **Which column do we wish to summarize?** Select the column containing values relevant to your question (like amount for revenue questions or distance for trip analysis).  

Examples:  

```sql
SELECT SUM(amount) AS `total_revenue`
FROM orders;

SELECT SUM(distance) AS `total_distance`
FROM trips;
```

2. **Which summary operation do we wish to apply?** Choose the appropriate operation based on the insight you need.  

* `SUM` for totals
* `AVG` for central tendency
* `MIN`/`MAX` for range
* `COUNT` for frequency

These questions form a framework for effectively designing data aggregation operations. They require understanding of the objective, the input data, and the domain — design first, then implement.

---

## #6 How Data is Organized

Databases are organized in a hierarchical structure. At the top level, a database contains multiple schemas (or datasets). Each schema contains multiple tables. Each table has rows and columns with defined data types.

In BigQuery specifically, this hierarchy uses the terminology: project → datasets → tables.

Tables are typically referenced using a qualified name format. In BigQuery, that's project.dataset.table. For example, `data-manipulation.ecommerce.orders` refers to the `orders` table in the `ecommerce` dataset within the `data-manipulation` project.

Schemas (or datasets in BigQuery) are like categories that help organize related tables.

Think of it this way:

* Project = your entire workspace or organization
* Dataset/Schema = a logical grouping of related tables (e.g., ecommerce, finance, user_analytics)
* Tables = the actual data within each category

For example, an ecommerce dataset might contain tables like orders, customers, products, and inventory—all related to e-commerce operations.

Creating datasets is typically done by database administrators or data engineers using BigQuery's interface or SQL commands like `CREATE SCHEMA`. As a Data Scientist, you'll mostly query existing datasets rather than create them—that's usually handled by the data infrastructure team.

---

## #7 Grouped Aggregation

* `SELECT DISTINCT` returns the unique values in a column.

```sql
SELECT DISTINCT column_name
FROM table_name;
```

* Before grouping, use `SELECT DISTINCT` to explore what groups exist in your data. This reveals unexpected categories and helps you anticipate issues before aggregation.

* Grouped aggregation calculates summary statistics for each distinct value in a grouping column, producing a summary table.

* Individual summary values tell us about the overall dataset, but grouped aggregation reveals patterns and variations across categories.

* The summary table has one row per group and one column per metric.

To design an effective grouped aggregation, answer three questions:

1. **By which column do you group?** The grouping column determines how rows are split into groups.
2. **Which column(s) do you summarize?** The input columns on which metrics are calculated.
3. **Which summary operation(s) do you apply?** Common operations include count, sum, mean, and unique count.

The `GROUP BY` clause, placed after `FROM`, groups rows by a specified column.

```sql
SELECT grouping_column,
       COUNT(column_1) AS total_count,
       SUM(column_2)   AS total_amount
FROM table_name
GROUP BY grouping_column;
```

The `SELECT` clause should include the grouping column and aggregate functions applied to other columns.

Alias each aggregate expression with `AS` for clarity.

Example #1: Get summary for each category.

```sql
SELECT category,
       COUNT(order_id)         AS order_count,
       COUNT(DISTINCT user_id) AS user_count,
       SUM(amount)             AS revenue,
       AVG(amount)             AS avg_amount
FROM orders
GROUP BY category;
```

Example #2: Get summary for each category, sorted by revenue (highest to lowest).

```sql
SELECT category,
       COUNT(order_id)         AS order_count,
       COUNT(DISTINCT user_id) AS user_count,
       SUM(amount)             AS revenue,
       AVG(amount)             AS avg_amount
FROM orders
GROUP BY category
ORDER BY revenue DESC;
```

Example #3: Get summary for each ride type.

```sql
SELECT ride_type,
       SUM(fare)      AS total_fare,
       AVG(fare)      AS average_fare,
       COUNT(trip_id) AS total_trips
FROM trips
GROUP BY ride_type;
```

Example #4: Get unique riders for each ride type.

```sql
SELECT ride_type,
       COUNT(DISTINCT rider_id) AS total_riders
FROM trips
GROUP BY ride_type
ORDER BY total_riders;
```

---

## #8 Grouped Aggregation Deep Dive - Split-Apply-Combine

Grouped aggregation works through a process called Split-Apply-Combine. This happens behind the scenes in three sequential steps.

Split -> Apply -> Combine

* **Split:** Data is divided into groups based on unique values in the grouping column.
* **Apply:** Summary operations are performed on each group separately.
* **Combine:** Results from each group are assembled into a single summary table.

**Step 1: Split**

The input data is broken down into groups based on unique values in the grouping column. Each group contains only rows with the same value in that column.

In our e-commerce example, when we grouped by category, SQL split the orders table into separate groups—one for Electronics orders, one for Fashion orders, one for Grocery orders, and one for Home & Garden orders.

The number of groups equals the number of unique values in the grouping column. Since our category column had four distinct values, we got four groups.

**Step 2: Apply**

Summary operations are performed on each group separately. The same operation can be applied to different columns, and different operations can be applied to the same column.

In our e-commerce example, SQL calculated the count, sum, and average independently for each category group. For the Electronics group, it counted Electronics orders, summed Electronics amounts, and averaged Electronics amounts—completely separate from the other groups.

This step creates intermediate results for each group, calculating the requested metrics independently within each subset of data.

**Step 3: Combine**

Results from each group are assembled into a single summary table. Each row represents one group from the splitting step, and each column represents one summary operation from the apply step.

In our e-commerce example, SQL took the intermediate results—Electronics metrics, Fashion metrics, Grocery metrics, and Home & Garden metrics—and combined them into one organized table with four rows.

This final step produces the complete summary table with all calculated metrics organized by group.

While this all happens behind the scenes when you use GROUP BY, understanding this process helps you think through what your queries will produce and anticipate the structure of your results.

---

## #9 Order of SQL Clauses

SQL requires clauses to be written in a specific order.

`SELECT` → `FROM` → `JOIN` → `WHERE` → `GROUP BY` → `HAVING` → `ORDER BY` → `LIMIT` → `OFFSET`

This order was established by SQL's creators as a language convention for readability — it allows queries to read naturally by stating what you want (SELECT) before specifying where it comes from (FROM), combining rows from additional tables (JOIN), narrowing down rows (WHERE), grouping them (GROUP BY), filtering groups (HAVING), sorting the result (ORDER BY), and finally paginating output (LIMIT / OFFSET).

Placing clauses out of order will produce a syntax error.

### Clause Breakdown

| Clause     | Purpose                                                |
|------------|--------------------------------------------------------|
| `SELECT`   | Specifies the columns to return                        |
| `FROM`     | Specifies the source table(s)                          |
| `JOIN`     | Combines rows from additional tables                   |
| `WHERE`    | Filters individual rows **before** grouping            |
| `GROUP BY` | Groups rows sharing a common value                     |
| `HAVING`   | Filters groups **after** aggregation                   |
| `ORDER BY` | Sorts the final result set                             |
| `LIMIT`    | Restricts the number of rows returned                  |
| `OFFSET`   | Skips a specified number of rows (used for pagination) |

### Key Distinctions to Remember

- **WHERE vs HAVING** — `WHERE` filters rows before grouping; `HAVING` filters after aggregation and can reference aggregate functions like `COUNT()` or `SUM()`.
- **LIMIT & OFFSET** — Not all databases use this syntax. MySQL, PostgreSQL, and SQLite use `LIMIT`/`OFFSET`; SQL Server uses `TOP` or `FETCH NEXT`; Oracle uses `ROWNUM` or `FETCH NEXT`.
- **JOIN** — Multiple `JOIN` clauses are all placed in the `FROM` block, before `WHERE`.

The most important addition to keep in mind is **WHERE vs HAVING**, since that's a very common point of confusion — `WHERE` can't reference aggregate functions, which is exactly why `HAVING` exists.

---

## #10 Grouping by Multiple Columns

* Multi-column aggregation enables cross-dimensional analysis, answering questions like "How does X vary across combinations of A and B?"

* To group by multiple columns, list the grouping columns in both the SELECT clause (to identify groups) and the GROUP BY clause (to define groups), separated by commas.

```sql
SELECT grouping_col1,
       grouping_col2,
       COUNT(column_1)          AS metric_1,
       COUNT(DISTINCT column_2) AS metric_2,
       SUM(column_3)            AS metric_3,
       AVG(column_3)            AS metric_4
FROM table_name
GROUP BY grouping_col1, grouping_col2;
```

**Incorrect `GROUP BY`**

Example #1: Incorrect `GROUP BY`

```sql
SELECT location,
       category,
       COUNT(order_id)         AS order_count,
       COUNT(DISTINCT user_id) AS user_count,
       SUM(amount)             AS revenue,
       AVG(amount)             AS avg_amount
FROM orders
GROUP BY location;
```

SELECT list expression references column category which is neither grouped nor aggregated at [2:8]

Example #2: Incorrect `GROUP BY`

```sql
SELECT location,
       COUNT(order_id) AS order_count
FROM orders
GROUP BY location, category;
```

It executes successfully but gives confusing results.

**Best practice**: Always include all grouping columns in your SELECT clause so results are clear and interpretable. If you're grouping by it, show it!

---

## #11 Metric Reliability

* Summaries based on small groups are often unreliable. Exceptional values in small groups are frequently anomalies that disappear as group size increases.

* Always include the group size (typically a count) in summary tables to evaluate which summary values can be trusted.

---

## #12 Trade-offs of Many Grouping Columns

* You can group by as many columns as needed for the desired level of detail.

* However, each additional grouping column increases the number of resulting groups.

* Too many grouping columns can lead to:
    1. An overwhelming number of summary values that are challenging to interpret
    2. Smaller group sizes that produce less reliable summary statistics

---

## #13 Handling Missing Groups

* Groups with no data will not appear in the summary table.

* Missing groups can occur for three reasons:
    1. Data Loss or Query Error: Data was lost, incompletely collected, or a bug in the query failed to capture existing combinations—something went wrong that needs investigation.
    2. No Instances Occurred: The combination is valid but had zero activity in the data—the data is correct.
    3. Impossible Combination: The combination cannot exist by definition.

---

## #14 Multi-Column Sorting

* We can sort by multiple columns with individual directions for each.

```sql
SELECT grouping_col1,
       grouping_col2,
       COUNT(column_1) AS metric_1,
       SUM(column_2)   AS metric_2
FROM table_name
GROUP BY grouping_col1, grouping_col2
ORDER BY grouping_col1 ASC, metric_2 DESC;
```

* SQL clauses must appear in this order: `SELECT` → `FROM` → `JOIN` → `WHERE` → `GROUP BY` → `HAVING` → `ORDER BY` → `LIMIT` → `OFFSET`.

---

## BigQuery

BigQuery is Google's serverless data warehouse, built to analyze massive amounts of data in seconds.

It has strong benefits:  

* **Industry Standard**: Widely adopted across startups and enterprises, making it a valuable skill in today's data-driven job market

* **Free to practice**: Google offers a generous free tier, so you can practice as much as you need without spending anything
