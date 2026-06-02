# Intermediate SQL - Conditional Data Transformation

**Index**  

Early if the flight arrives more than 5 minutes before schedule
Late if it arrives more than 5 minutes after schedule
On_time for everything else

```sql
-- Get an average distance of Early flights
SELECT AVG(distance)
FROM flights
WHERE delay < -5;

-- Get an average distance of Late flights
SELECT AVG(distance)
FROM flights
WHERE delay > 5;

-- Get an average distance of On_Time flights
SELECT AVG(distance)
FROM flights
WHERE (delay >= -5) AND (delay <= 5);


WITH flights_step1 AS (
    SELECT *,
        -- this is because to convert all the distances into km (same unit)
        IF(distance_unit = 'miles', distance * 1.60934, distance) AS distance_km,
        -- adding a new column for the flight status
        IF(
            delay < -5, 'Early',
            IF (delay > 5, 'Late', 'On time')
        ) AS delay_status
    FROM flights
)
SELECT delay_status, AVG(distance_km) AS avg_distance
FROM flights_step1
GROUP BY delay_status;



```

IF function Vs CASE WHEN
CASE WHEN is supported by almost all the SQL flavours (MySQL, PostgreSQL, BigQuery etc.).
Also it is easy to scale if in future we need to check multiple conditions.
CASE WHEN more readable and structured compared to IF.


Question: What percentage of flights operate widebody vs narrowbody aircraft?

To start with lets finalize the data summary that we need.

We need the following summary at the end:

aircraft_type - operate_pot
wide_body     - 70.55
narrow_body   - 29.45

Step #1
Transformation
We will start with transformation to add a new column aircraft_type.

We need to classify aircrafts between - wide body & narrow body.
wide body when total_seats >= 300
else narrow body

We can use transformation to do this.

Add a new column aircraft_type.
It can be wide_body or narrow_body.

```sql
SELECT *,
    CASE
        WHEN total_seats >= 300 THEN 'wide_body'
        ELSE 'narrow_body'
    END AS aircraft_type
FROM flights;
```

Step #2
Grouping
Second step is to group by aircraft_type and get count of each type.
After that we can modify the same query to get operate_pot, where operate_pot = count of each type / total flights

```sql
-- first lets get the count for each type
WITH flights_step1 AS (
    SELECT *,
        CASE
            WHEN total_seats >= 300 THEN 'wide_body'
            ELSE 'narrow_body'
        END AS aircraft_type
    FROM flights
)
SELECT COUNT(flight_id)
FROM flights_step1
GROUP BY aircraft_type;

-- second lets lets modify the same query to get operate_pot
WITH flights_step1 AS (
    SELECT flight_id,
           airline,
           total_seats,
           passengers,
           CASE
               WHEN total_seats >= 300 THEN 'Widebody'
               ELSE 'Narrowbody'
           END AS aircraft_size
    FROM flights
)
SELECT aircraft_size,
    COUNT(flight_id) AS number_of_flights,
    ROUND(100 * COUNT(flight_id) / NULLIF((SELECT COUNT(flight_id) FROM flights), 0), 2) AS percentage_of_flights
FROM flights_step1
GROUP BY aircraft_size;
```




What transformation logic do we need?

We want to create a new column called delay_status based on these conditions:

If delay ≤ -5 → assign Early
If delay > -5 and < 5 → assign On time
If delay ≥ 5 → assign Late

```sql
SELECT *,
    CASE
        WHEN delay <= -5 THEN 'Early'
        WHEN delay >= 5 THEN 'Late'
        ELSE 'On time'
    END AS delay_status
FROM flights;
```






Flights are categorized as:

Full: Occupancy ≥ 90% (nearly every seat is taken)
High: Occupancy ≥ 70% (well-filled but not packed)
Low: Occupancy < 70% (underutilized with many empty seats)
This analysis helps with capacity planning and pricing decisions.

Question: What percentage of flights fall into each occupancy category?

First transformation and then aggregation

Step #1 Transformation
We do transformation step to get a new column occupancy.

First we need to get the occupancy percentage,
we can do that using the formula: 100 * passengers / total_seats

After we get occupancy percentage we need to set its category based on its value.

Full: Occupancy percentage ≥ 90%
High: Occupancy percentage ≥ 70%
Low: Occupancy percentage < 70%

We can write two transformation steps but we can do this in one step as well as follows:

```sql
CASE
    WHEN ROUND(100 * passengers / NULLIF(total_seats, 0), 2) >= 90 THEN 'Full'
    WHEN ROUND(100 * passengers / NULLIF(total_seats, 0), 2) >= 70 THEN 'High'
    ELSE 'Low'
END AS 'occupancy'
```

Step #2 Aggregation
Then we aggregate results based on occupancy.
Also to get percentage of flights for each category we will get occupany counts and total counts (using a scalar subquery) and then do the calculation.

---

multiple sequential steps

Often, a transformation requires multiple sequential steps where each step builds on the previous one. For example, in your future ML work, you might need to calculate a feature, then normalize it, then categorize it—each step depending on the last.

SQL handles this by allowing **multiple CTEs to be chained together**, separated by commas. Each CTE can reference the ones defined before it:

Key points:

* Use `WITH` only once at the beginning
* Separate each CTE with a comma
* Each CTE can reference any CTE defined above it
* The final `SELECT` queries from whichever CTE has what you need

```sql
WITH step_one AS (
    SELECT *,
           <first_calculation> AS intermediate_result
    FROM original_table
), step_two AS (
    SELECT *,
           <calculation_using_intermediate_result> AS final_result
    FROM step_one
)
SELECT *
FROM step_two;
```

---

CTE = Common Table Expression

**CTE** stands for **Common Table Expression**. It's essentially a temporary, named result set that you define at the beginning of a query and can reference within that same query.

Think of it like creating a temporary "virtual table" that exists only for the duration of the query.

**Basic syntax:**  

```sql
WITH cte_name AS (
    SELECT ...
    FROM ...
)
SELECT *
FROM cte_name;
```

**Why use CTEs?**

- **Readability** — break a complex query into logical, named steps instead of nesting subqueries inside each other
- **Reusability** — reference the same CTE multiple times in one query instead of repeating the subquery
- **Chaining** — build step-by-step logic, where each CTE builds on the previous one (like in your flights example)

**CTE vs alternatives:**

| | CTE | Subquery | Temp Table |
|---|---|---|---|
| Readability | ✅ High | ❌ Low | ✅ High |
| Reusable in same query | ✅ Yes | ❌ No | ✅ Yes |
| Persists after query | ❌ No | ❌ No | ✅ Yes |
| Needs CREATE privilege | ❌ No | ❌ No | ✅ Yes |
