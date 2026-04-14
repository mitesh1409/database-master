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

