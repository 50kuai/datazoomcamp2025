# datazoomcamp2025_homework 2

Question 3. Number of rows (yellow, 2020)
SELECT COUNT(*)
FROM yellow_tripdata
WHERE filename LIKE 'yellow_tripdata_2020-%';
<img width="677" alt="Screenshot 2025-02-04 at 9 06 46 PM" src="https://github.com/user-attachments/assets/ceca427c-f813-4f57-a05a-498d011816cf" />


Question 4. Number of rows (green, 2020)
SELECT COUNT(*)
FROM green_tripdata
WHERE filename LIKE 'green_tripdata_2020-%';
<img width="705" alt="Screenshot 2025-02-04 at 9 02 24 PM" src="https://github.com/user-attachments/assets/51c60977-b2a5-4652-8d8c-aab7749f90bf" />



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
    green_taxi_data
WHERE 
    lpep_dropoff_datetime >= '2019-10-01 00:00:00' -- Include trips ending on or after October 1, 2019
    AND lpep_dropoff_datetime < '2019-11-01 00:00:00' -- Exclude trips ending on or after November 1, 2019
GROUP BY 
    distance_range
ORDER BY 
    distance_range;
```
<img width="1225" alt="Screenshot 2025-01-25 at 11 32 55 AM" src="https://github.com/user-attachments/assets/5d1e0c05-5020-49c3-af06-ca517002f430" />

---

### **Question 4: Longest Trip**

This query identifies the pickup day with the longest trip distance.

```sql
SELECT 
    DATE(lpep_pickup_datetime) AS pickup_day, 
    MAX(trip_distance) AS longest_trip_distance
FROM 
    green_taxi_data
GROUP BY 
    lpep_pickup_datetime
ORDER BY 
    longest_trip_distance DESC;
```
<img width="873" alt="Screenshot 2025-01-25 at 11 33 44 AM" src="https://github.com/user-attachments/assets/b7cedce9-5a38-482a-bddd-7a1c053715be" />

---

### **Question 5: Biggest Pickup Zones**

This query finds the pickup zones with total earnings exceeding $13,000 on October 18, 2019, along with their zone names.

```sql
SELECT 
    "Zone", 
    "PULocationID", 
    SUM(gt.total_amount) AS total_amount_for_day
FROM 
    green_taxi_data gt
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
<img width="932" alt="Screenshot 2025-01-25 at 11 34 07 AM" src="https://github.com/user-attachments/assets/75e8e9f0-2658-4af9-b618-73d09fe59a5a" />

---

### **Question 6: Largest Tip**

This query finds the drop-off zone with the largest tip amount for trips picked up in the "East Harlem North" zone during October 2019.

```sql
SELECT 
    tz_drop."Zone" AS dropoff_zone, 
    MAX(gt."tip_amount") AS largest_tip
FROM 
    green_taxi_data gt
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
<img width="1021" alt="Screenshot 2025-01-25 at 11 34 31 AM" src="https://github.com/user-attachments/assets/70fc8878-32fd-47cb-8358-e9a4e6f871c3" />
