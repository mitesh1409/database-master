# Filtering

**Index**  

1. Filtering in SQL
2. Strings in SQL
3. Logical Expressions
4. Partial String Matching
5. Missing Values
6. Multiple Conditions
7. Top/Bottom N Filtering
8. Intermediate Columns
9. Result Verification
10. Direct Calculation Vs Creating Intermediate Columns
11. Operator Precedence - Logical Operators - `NOT`, `AND`, `OR`
12. Inverting a Comparison Condition
13. Inverting a Compound Condition

---

## #1 Filtering in SQL

* We apply filtering to our data to extract only those rows that are relevant for the purpose at hand.

* To filter rows in SQL, we use the `WHERE` clause with a condition.

```sql
SELECT col_2,
       col_3
FROM table_1
WHERE col_1 = 'value';
```

**How Filtering Actually Works Under The Hood**  

Every filtering condition you write is called a logical expression — an expression that evaluates to either True or False.

Logical expressions have three parts:

* Left-hand side (LHS): Usually a column reference (like profit or market_cap)
* Logical operator: A comparison operation (=, !=, <, >, <=, >=)
* Right-hand side (RHS): A value to compare against (like 0 or 1000000)

Two-step process happens behind the scenes every time you filter:  

**Step 1: Condition Evaluation**  

SQL evaluates `WHERE` clause condition for each row.  
This creates what's called Boolean data — values that are either True or False.

**Step 2: Row Selection**  

Now SQL selects only the rows where the condition evaluated to True.

Example: Get companies with market cap greater than 1000000.

```sql
SELECT company,
       sector,
       rank_2025,
       revenue,
       profit,
       market_cap
FROM fortune_50
WHERE market_cap > 1000000;
```

In your query, `market_cap > 1000000` is a logical expression. For each row, SQL evaluates whether that company's market cap is greater than 1,000,000 — returning True or False.

---

## #2 Strings in SQL

* String values are text data, as opposed to numeric data.

* In SQL, string values must be enclosed in quotes — either single quotes (') or double quotes (").

* Single quotes work in all SQL databases, while double quotes only work in some.

In SQL, string values (text data like company names or sector categories) must be enclosed in quotes.

You can use either single quotes (') or double quotes ("), but there's an important difference:

* Single quotes work in all SQL databases (BigQuery, PostgreSQL, MySQL, Oracle, etc.)
* Double quotes only work in some databases (like BigQuery)
* Since single quotes ensure your queries work everywhere, it's best practice to use them.

---

## #3 Logical Expressions

* Filtering uses logical expressions that evaluate to `TRUE` or `FALSE`.

* A logical expression has three parts: column reference, comparison operator, and value.

**Comparison Operators**  

The comparison operators you can use in logical expressions.

Here are the six main operators:

* Equal to (`=`): Checks if values are the same (e.g., `sector = 'Technology'`)
* Not equal to (`!=` or `<>`): Checks if values are different (e.g., `sector != 'Retail'`)
* Less than (`<`): Checks if left is smaller (e.g., `profit < 0` for losses)
* Greater than (`>`): Checks if left is larger (e.g., `market_cap > 1000000` for trillion-dollar companies)
* Less than or equal to (`<=`): Left is smaller or equal (e.g., `rank_2025 <= 10` for top 10)
* Greater than or equal to (`>=`): Left is larger or equal (e.g., `employees >= 100000` for large employers)

---

## #4 Partial String Matching

* To filter rows where a column contains a pattern, use `LIKE` with `%` wildcards.

```sql
SELECT *
FROM table_1
WHERE col_1 LIKE '%value%';
```

* The `%` is a wildcard that matches any sequence of characters:
    * `%value%`: contains 'value' anywhere
    * `value%`: starts with 'value'
    * `%value`: ends with 'value'

---

## #5 Missing Values

* Missing values are represented as `NULL` in SQL.

* To filter rows where a column is `NULL` (missing), use `IS NULL`.

```sql
SELECT *
FROM table_1
WHERE col_1 IS NULL
```

* To filter rows where a column has a value, use `IS NOT NULL`.

```sql
SELECT *
FROM table_1
WHERE col_1 IS NOT NULL
```

---

## #6 Multiple Conditions

**How SQL executes multiple conditions?**  

The step-by-step process of how SQL executes multiple conditions behind the scenes:

Step 1: Condition Evaluation  
First, each filtering condition is evaluated separately for each row, producing True or False values.

Step 2: Combining Conditions  
Next, these True/False values are combined using the logical AND/OR operation in the combined column.

Step 3: Row Selection  
Finally, only rows where the combined result is True are selected.

To recap, the filtering process follows three clear steps:

1. Evaluate each condition separately for every row (`TRUE`/`FALSE`)
2. Combine the results using the logical operator (`AND`/`OR`)
3. Select only the rows where the final combined result is `TRUE`

**Framework to write filtering conditions**

Before writing code, take a minute to think systematically through your filtering conditions using this framework:

(1) What filtering conditions do we need to apply?  
Define each individual condition clearly.

(2) How do we combine the conditions?  
Determine the logical relationship between conditions:  
* Use `AND` when all criteria must be satisfied
* Use `OR` when any criterion is sufficient

This systematic thinking prevents logical errors and ensures your filters work as intended.

**Short-circuiting**

Short-circuiting is indeed a common optimization in programming languages.

In SQL, the behavior varies by database system. BigQuery and most modern SQL databases do perform short-circuit evaluation as an optimization:

* **AND**: stops evaluating as soon as it finds a False condition
* **OR**: stops evaluating as soon as it finds a True condition

However, there's an important caveat: you generally shouldn't rely on this behavior in SQL. Unlike procedural programming languages where execution order is guaranteed, SQL is a declarative language—you describe what you want, and the database optimizer decides how to execute it most efficiently. The optimizer might reorder conditions, evaluate them in parallel, or use entirely different execution strategies.

For your work as a Data Scientist, the key takeaway is: write clear, correct conditions without depending on evaluation order. The database will handle the optimization.

**AND and OR Operations**

* We can combine multiple filtering conditions using `AND` and `OR` operations.
* The `AND` operation returns rows that satisfy all the conditions.
* The `OR` operation returns rows that satisfy any one condition.

**Logical Operator Precedence**

* By default, `AND` operations are executed before `OR` operations.
* We can use parentheses to enforce a particular order of execution.

**Design Framework**

We developed the following framework for designing filtering operations:

1. What filtering conditions do we need to apply?
2. If applicable, how do we combine multiple conditions?

---

## #7 Top/Bottom N Filtering

To get "top N" or "bottom N" records:

* Use `DESC` for highest values (top performers)
* Use `ASC` or omit it for lowest values (bottom performers)
* Adjust the number in `LIMIT` to get however many rows you need

Examples: Five companies with the fewest employees.

```sql
SELECT company,
       market_cap,
       sector,
       state,
       employees
FROM fortune_50
ORDER BY employees ASC
LIMIT 5;
```

Example: The 10 most profitable companies.

```sql
SELECT company,
       profit,
       revenue
FROM fortune_50
ORDER BY profit DESC
LIMIT 10;
```

---

## #8 Intermediate Columns

Following does not work, why?

An error occurred! The error message says `Unrecognized name: is_top10`.

```sql
SELECT company,
       rank_2025,
       rank_2024,
       sector,
       profit,
       rank_2025 <= 10       AS is_top10,
       profit > 0            AS is_profitable,
       rank_2025 < rank_2024 AS is_rank_improved
FROM fortune_50
WHERE is_top10
    AND (is_profitable OR is_rank_improved);
```

Here's why this happens: SQL evaluates clauses in a specific order that affects how we can use columns:

* `FROM`: SQL first identifies which table(s) to work with
* `WHERE`: Next, it evaluates filtering conditions on the existing columns
* `SELECT`: Finally, it creates new columns or selects which columns to include

Key implication: Since `WHERE` is evaluated before `SELECT`, any columns created in the `SELECT` clause aren't yet available for use in the `WHERE` clause.

**Intermediate Columns**

The `WITH` clause overrides this order by creating the intermediate columns in a separate query first, making them available for the main query to reference.

As we learned earlier in transformations:  

* SQL cannot reference a column alias in the same `SELECT` where it's defined  
* Use the `WITH` clause to break complex transformations into named steps  

So the correct query is:  

```sql
WITH step_1 AS (
    SELECT company,
           rank_2025,
           rank_2024,
           sector,
           profit,
           rank_2025 <= 10       AS is_top10,
           profit > 0            AS is_profitable,
           rank_2025 < rank_2024 AS is_rank_improved
    FROM fortune_50
)
SELECT company,
       rank_2025,
       rank_2024,
       sector,
       profit,
       is_top10,
       is_profitable,
       is_rank_improved
WHERE is_top10
    AND (is_profitable OR is_rank_improved);
```

> Creating meaningful boolean columns is a powerful pattern that makes data easier to work with across many operations.

* Break complex filtering conditions into named boolean columns for clarity and verification

* Use `WITH` to create intermediate columns first, then filter — needed because `WHERE` is evaluated before `SELECT`:

```sql
WITH enriched_table AS (
    SELECT *,
           col_1 <= a    AS condition1,
           col_2 > b     AS condition2,
           col_1 < col_3 AS condition3
    FROM table_1
)
SELECT *
FROM enriched_table
WHERE condition1
    AND (condition2 OR condition3);
```

---

## #9 Result Verification

Complex filtering conditions are prone to errors, and with large datasets, mistakes aren't immediately obvious. Systematic verification catches these errors before they lead to incorrect conclusions.

Whether you're preparing training data for ML models or validating user segments for analysis, verifying your filters ensures you're working with the right data.

Verification happens at two stages:

1. While building — Verify each intermediate column works correctly on its own
2. After building — Verify the final filtered result includes the right rows and excludes the right rows

---

## #10 Direct Calculation Vs Creating Intermediate Columns

Example: We want to find companies that are either in the top 10 in the 2025 ranking OR whose profit is more than 25% of their revenue.

```sql
SELECT company,
       rank_2025,
       rank_2024,
       sector,
       profit,
       revenue
FROM fortune_50
WHERE rank_2025 <= 10
    OR profit / revenue > 0.25;
```

Direct calculation in filtering means using arithmetic expressions or calculations directly within filtering conditions, rather than creating separate columns for the calculated values.

This is useful when you need to filter by a calculated metric — like profitability ratio, growth rate, or efficiency metrics. In your future ML work, you might filter training data based on calculated feature values or threshold ratios.

When to use direct calculation:

* The calculation is simple and easy to read
* The calculated value is only needed for filtering, not for display
* The stakes are low and the condition is simple enough that verification isn't a concern

When to use intermediate columns:

* The calculation is complex or involves multiple operations
* The calculated value will be used multiple times or displayed in results
* You're dealing with important business logic where stakes are high

In those cases, you need the filtering criteria visible in results for verification. When preparing training data for ML models or validating critical business rules, seeing the actual calculated values helps catch errors before they propagate.

Default guidance: When in doubt, use intermediate columns. The verification benefits usually outweigh the small extra effort.

---

## #11 Operator Precedence - Logical Operators - `NOT`, `AND`, `OR`

Operator precedence — the order in which SQL evaluates logical operators:

1. `NOT` — Executed first (highest precedence)
2. `AND` — Executed second
3. `OR` — Executed last (lowest precedence)

---

## #12 Inverting a Comparison Condition

Comparison operator <=> Inverse of it
`=`  <=> `!=`  
`>`  <=> `<=`  
`>=` <=> `<`  
`<`  <=> `>=`  
`<=` <=> `>`  

To invert a comparison, find its complement — the condition that captures exactly the opposite cases.

For instance, `NOT (x = y)` becomes `x ≠ y`. If something equals y, its complement is everything that doesn't equal y.

The same logic applies to inequalities:

* `NOT (x > y)` → `x ≤ y` (if not greater, then less than or equal)
* `NOT (x ≥ y)` → `x < y` (if not greater or equal, then strictly less)
* `NOT (x < y)` → `x ≥ y` (if not less, then greater or equal)
* `NOT (x ≤ y)` → `x > y` (if not less or equal, then strictly greater)

Think of it as flipping to the opposite side of the boundary.

---

## #13 Inverting a Compound Condition

For compound conditions, inversion works as follows:

* `NOT (A AND B)` → `NOT A OR NOT B`
* `NOT (A OR B)` → `NOT A AND NOT B`

Each part gets inverted (using the comparison rules we just covered), and AND/OR swap.

> Break the line - NOT is applied to all operands in the condition
> change the sign - AND -> OR, OR -> AND, AND <-> OR (swap)

This principle is called **De Morgan's Law** — a fundamental rule in logic that you'll encounter in data validation, feature engineering, and building complex filtering conditions.
