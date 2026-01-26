Question 1. What's the version of pip in the python:3.13 image? (1 point)
```bash
docker run -it --rm python:3.13 bash
pip --version
```
Question 3. Counting short trips
For the trips in November 2025 (lpep_pickup_datetime between '2025-11-01' and '2025-12-01', exclusive of the upper bound), how many trips had a trip_distance of less than or equal to 1 mile?
```sql
SELECT COUNT(*) FROM yellow_taxi_data WHERE lpep_pickup_datetime between '2025-11-01' and '2025-12-01' AND trip_distance <= 1
```
Question 4. Longest trip for each day
Which was the pick up day with the longest trip distance? Only consider trips with trip_distance less than 100 miles (to exclude data errors).

Use the pick up time for your calculations.
```sql
SELECT lpep_pickup_datetime::date as pickup_date
FROM yellow_taxi_data 
WHERE trip_distance = (
        SELECT MAX(trip_distance) 
        FROM yellow_taxi_data 
        WHERE trip_distance <= 100
) 
LIMIT 1
```
Question 5. Biggest pickup zone
Which was the pickup zone with the largest total_amount (sum of all trips) on November 18th, 2025?
```sql
SELECT tzl."zone",SUM(ytd.total_amount) as total_trip
FROM yellow_taxi_data ytd
JOIN taxi_zone_lookup tzl ON tzl.locationid = ytd."PULocationID"
WHERE ytd.lpep_pickup_datetime::date = '2025-11-18'
GROUP BY tzl."zone"
ORDER BY total_trip DESC
LIMIT 1
```
Question 6. Largest tip
For the passengers picked up in the zone named "East Harlem North" in November 2025, which was the drop off zone that had the largest tip?

Note: it's tip , not trip. We need the name of the zone, not the ID.
```sql
SELECT tzldo."zone" as dropoff_zone,MAX(ytd.tip_amount) as max_tip
FROM yellow_taxi_data ytd
JOIN taxi_zone_lookup tzl ON tzl.locationid = ytd."PULocationID" 
JOIN taxi_zone_lookup tzldo ON tzldo.locationid = ytd."DOLocationID"
WHERE tzl."zone" = 'East Harlem North'
  AND ytd.lpep_pickup_datetime::date >= DATE '2025-11-01'
  AND ytd.lpep_pickup_datetime::date <  DATE '2025-12-01'
GROUP BY tzldo."zone"
ORDER BY max_tip DESC
LIMIT 1
```