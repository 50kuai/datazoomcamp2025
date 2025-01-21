# datazoomcamp2025_homework 1

# SQL Queries for Analysis

### **Question 3: Trip Segmentation Count**

This query segments trips by distance and counts the number of trips in each range for trips completed between October 1, 2019, and October 31, 2019.

```sql
SELECT 
    CASE
        WHEN trip_distance <= 1.0 THEN 'Up to 1 mile'
        WHEN trip_distance > 1.0 AND trip_distance <= 3.0 THEN 'Between 1 and 3 miles'
        WHEN trip_distance > 3.0 AND trip_distance <= 7.0 THEN 'Between 3 and 7 miles'
        WHEN trip_distance > 7.0 AND trip_distance <= 10.0 THEN 'Between 7 and 10 miles'
        WHEN trip_distance > 10.0 THEN 'Over 10 miles'
    END AS distance_range,
    COUNT(*) AS trip_count
FROM 
    green_taxi
WHERE 
    lpep_dropoff_datetime >= '2019-10-01 00:00:00' -- Include trips ending on or after October 1, 2019
    AND lpep_dropoff_datetime < '2019-11-01 00:00:00' -- Exclude trips ending on or after November 1, 2019
GROUP BY 
    distance_range
ORDER BY 
    distance_range;
```

---

### **Question 4: Longest Trip**

This query identifies the pickup day with the longest trip distance.

```sql
SELECT 
    DATE(lpep_pickup_datetime) AS pickup_day, 
    MAX(trip_distance) AS longest_trip_distance
FROM 
    green_taxi
GROUP BY 
    lpep_pickup_datetime
ORDER BY 
    longest_trip_distance DESC;
```

---

### **Question 5: Biggest Pickup Zones**

This query finds the pickup zones with total earnings exceeding $13,000 on October 18, 2019, along with their zone names.

```sql
SELECT 
    "Zone", 
    "PULocationID", 
    SUM(gt.total_amount) AS total_amount_for_day
FROM 
    green_taxi gt
JOIN 
    taxi_zone tz 
    ON "PULocationID" = "LocationID" 
WHERE 
    DATE(gt.lpep_pickup_datetime) = '2019-10-18' 
GROUP BY 
    "PULocationID", "Zone"
HAVING 
    SUM(gt.total_amount) > 13000 
ORDER BY 
    total_amount_for_day DESC;
```

---

### **Question 6: Largest Tip**

This query finds the drop-off zone with the largest tip amount for trips picked up in the "East Harlem North" zone during October 2019.

```sql
SELECT 
    tz_drop."Zone" AS dropoff_zone, 
    MAX(gt."tip_amount") AS largest_tip
FROM 
    green_taxi gt
JOIN 
    taxi_zone tz_pickup
    ON gt."PULocationID" = tz_pickup."LocationID"
JOIN 
    taxi_zone tz_drop
    ON gt."DOLocationID" = tz_drop."LocationID"
WHERE 
    DATE(gt."lpep_pickup_datetime") BETWEEN '2019-10-01' AND '2019-10-31'
    AND tz_pickup."Zone" = 'East Harlem North'
GROUP BY 
    tz_drop."Zone"
ORDER BY 
    largest_tip DESC
LIMIT 1;
```
