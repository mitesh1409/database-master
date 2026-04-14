# Intermediate SQL - Data Transformation

**Index**  

1. Data Transformation
2. Data Transformation in SQL
3. Calculating Ratios
4. `WITH` Clause for Multi-Step Calculations
5. Scalar Subqueries for Aggregate Values
6. Calculating Percent of Total
7. Rounding and Transformation Functions

---

## #1 Data Transformation

* Data Transformation is a fundamental data manipulation operation that allows us to derive new columns from existing columns.

* With data transformation, the output table is a copy of the original input table with the new columns attached at the end. This is in contrast to data aggregation, which summarizes the data, providing a higher level, less granular view.

---

## #2 Data Transformation in SQL

* We can create new columns in SQL by adding expressions in the `SELECT` statement.

For example, creating a new column to show USD into EUR.

```sql
SELECT *,
       col_usd * 0.92 AS col_eur
FROM table_name;
```

* We can use any standard arithmetic operator to combine columns, as well as any SQL function.

* Use parentheses to group operations for clarity and to control operator precedence. When in doubt, add parentheses.

* Instead of `SELECT *` to select all columns, we can specify specific columns to include.

---

## #3 Calculating Ratios

* Ratios measure relationships between different quantities and are calculated by dividing one column value by another.

* Division by zero is a common error in calculations.

* Use `NULLIF` to handle division by zero gracefully:

```sql
SELECT *,
       col_a / NULLIF(col_b, 0) AS ratio
FROM table_name;
```

* Or use `SAFE_DIVIDE` to handle division by zero in BigQuery:

```sql
SELECT *,
       SAFE_DIVIDE(col_a, col_b) AS ratio
FROM table_name;
```

**Beware of sample size when interpreting ratios**  

Always consider the sample size when interpreting ratios. Small denominators produce volatile, unreliable metrics that can mislead you.

Small sample sizes amplify noise and make ratios unreliable. Always check the denominator before celebrating (or panicking about) a metric.

---

## #4 `WITH` Clause for Multi-Step Calculations

* SQL cannot reference a column alias in the same `SELECT` where it's defined

* Use the `WITH` clause to break complex transformations into named steps

* Each step can reference columns created in previous steps

```sql
WITH step_1 AS (
    SELECT *,
           (col_a * col_b) - col_c AS intermediate
    FROM table_name
)
SELECT *,
       SAFE_DIVIDE(100 * intermediate, col_a * col_b) AS ratio
FROM step_1;
```

Here's the complete picture of how the `WITH` clause works:

* The `WITH` clause creates a named temporary result from a query

* The temporary table contains all original columns plus any new calculated columns

* The final `SELECT` queries from the temporary table, not the original table

* Column aliases from the `WITH` step can be used directly in the final `SELECT`

This is the clean, professional way to handle multi-step transformations in SQL.

Example:

The original campaign_performance table has the following columns:
campaign_day, ad_spend, paid_sessions, paid_orders, revenue, avg_fulfillment_cost

```sql
WITH campaign_performance_extended AS (
    SELECT *,
           revenue - ad_spend - (paid_orders * avg_fulfillment_cost) AS ad_contribution
    FROM campaign_performance
)
SELECT *,
       SAFE_DIVIDE(100 * ad_contribution, revenue) AS contribution_rate
FROM campaign_performance_extended;
```

When you break complex transformations into steps—creating columns for intermediate results — your code becomes:

1. **Easier to write** — Each step is simpler, reducing errors during implementation
2. **Easier to read** — Others (and future you) can follow the logic
3. **Easier to debug** — You can inspect intermediate values to find where things went wrong

> The old math advice "Show your work" holds true!

When you identify a complex formula, look for natural breakpoints—places where an intermediate result has meaning on its own. Name these columns descriptively so the logic is self-documenting.

---

## #5 Scalar Subqueries for Aggregate Values

* A scalar subquery is a query nested inside another query that returns a single value

* It's called "scalar" because it returns just one value (a scalar), not a table or multiple rows

* Aggregate functions like `SUM()` collapse rows, so they can't be used directly in row-level calculations

* A scalar subquery calculates the aggregate separately and returns a single value that each row can use

```sql
SELECT *,
       col_a - (SELECT AGG_FUNC(col_a) FROM table_name) AS result
FROM table_name;
```

---

## #6 Calculating Percent of Total

* Percent of total normalizes values to a common scale, making relative size immediately clear

* Divide each value by the sum (via scalar subquery) and multiply by 100

NOTE:  
> Negative numbers can cause percentage totals to exceed 100%. Always check if negative categories affect totals before interpreting percentages.

```sql
SELECT *,
       100 * col_a / (SELECT SUM(col_a) FROM table_name) AS col_a_pot
FROM table_name;
```

---

## #7 Rounding and Transformation Functions

* Use the `ROUND()` function to control decimal places for readability

* Rounding is one example of many transformation functions; AI assistants can help you find the right function for your needs

```sql
SELECT *,
       ROUND(100 * col_a / col_b, 2) AS percentage
FROM table_name;
```
