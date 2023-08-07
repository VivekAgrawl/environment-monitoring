## Analysis using SQl

#### Task 1. Find the average temperature recorded for each device. The task is to calculate the average temperature recorded for each device in the dataset.

<pre>
SELECT device_id, AVG(temperature)
FROM cleaned_environment
GROUP BY device_id;
</pre>

#### Task 2. Retrieve the top 5 devices with the highest average carbon monoxide levels. This task involves identifying the devices with the highest average carbon monoxide levels and retrieving the top 5 devices based on this metric.

<pre>
SELECT device_id, AVG(carbon_monoxide) as avg_co
FROM cleaned_environment
GROUP BY device_id
ORDER BY avg_co
LIMIT 5;
</pre>

#### Task 3. Calculate the average temperature recorded in the cleaned_environment table. The objective is to Determine the average temperature recorded in the cleaned_environment dataset.

<pre>
SELECT AVG(temperature)
FROM cleaned_environment;
</pre>

#### Task 4. Find the timestamp and temperature of the highest recorded temperature for each device. This task requires identifying the highest recorded temperature for each device and retrieving the corresponding timestamp and temperature values.

<pre>
SELECT timestamp, device_id, MAX(temperature)
FROM cleaned_environment
GROUP BY device_id, timestamp;
</pre>

#### Task 5. Identify devices where the temperature has increased from the minimum recorded temperature to the maximum recorded temperature.  The goal is to Identify devices where the temperature has increased from the minimum recorded temperature to the maximum recorded temperature.

<pre>
SELECT device_id
FROM cleaned_environment
GROUP BY device_id
HAVING MIN(temperature) < MAX(temperature);
</pre>

#### Task 6. Find the timestamps and devices where carbon monoxide level exceeds the average carbon monoxide level of all devices. The objective is to identify the timestamps and devices where the carbon monoxide level exceeds the average carbon monoxide level across all devices.

<pre>
SELECT timestamp, device_id
FROM cleaned_environment
WHERE carbon_monoxide > (SELECT AVG(carbon_monoxide) FROM cleaned_environment);
</pre>

#### Task 7. Retrieve the devices with the highest average temperature recorded. The objective is to identify the devices that have recorded the highest average temperature among all the devices in the dataset.

<pre>
SELECT device_id, AVG(temperature)
FROM cleaned_environment
GROUP BY device_id
ORDER BY 2 desc;
</pre>

#### Task 8. Calculate the average temperature for each hour of the day across all devices. The goal is to calculate the average temperature for each hour of the day, considering data from all devices.

<pre>
SELECT hour(timestamp), AVG(temperature)
FROM cleaned_environment
GROUP BY 1;
</pre>

#### Task 9. Which device(s) in the cleaned environment dataset have recorded only a single distinct temperature value? The objective is to identify device(s) in the cleaned environment dataset have recorded only a single distinct temperature value.

<pre>
SELECT device_id
FROM cleaned_environment
GROUP BY device_id
HAVING COUNT(DISTINCT temperature) = 1;
</pre>

#### Task 10. Find the devices with the highest humidity levels. The objective is to identify the devices that have recorded the highest humidity levels.

<pre>
SELECT device_id, MAX(humidity)
FROM cleaned_environment
GROUP BY device_id
ORDER BY 2 DESC
</pre>

#### Task 11. Calculate the average temperature for each device, excluding outliers (temperatures beyond 3 standard deviations). This task requires calculating the average temperature for each device while excluding outliers, which are temperatures beyond 3 standard deviations from the mean.

<pre>
SELECT device_id, AVG(temperature) AS average_temperature
FROM cleaned_environment
WHERE temperature BETWEEN (
    SELECT AVG(temperature) - 3 * STDDEV(temperature)
    FROM cleaned_environment
  ) AND (
    SELECT AVG(temperature) + 3 * STDDEV(temperature)
    FROM cleaned_environment
  )
GROUP BY device_id;
</pre>

#### Task 12. Retrieve the devices that have experienced a sudden change in humidity (greater than 50% difference) within a 30-minute window. The goal is to identify devices that have undergone a sudden change in humidity, where the difference is greater than 50%, within a 30-minute time window.

<pre>
WITH T1 AS (
  SELECT device_id, timestamp, humidity,
    LEAD(humidity) OVER (PARTITION BY device_id ORDER BY timestamp) AS lead_humidity
  FROM cleaned_environment
),
T2 AS (
  SELECT device_id, timestamp, humidity, lead_humidity,
    ABS(lead_humidity - humidity) AS change_humidity
  FROM T1
)
SELECT device_id, timestamp, lead_humidity
FROM T2
WHERE change_humidity > 0.5
  </pre>

#### Task 13. Find the average temperature for each device during weekdays and weekends separately. This task involves calculating the average temperature for each device separately for weekdays and weekends.

<pre>
SELECT device_id,
  CASE
    WHEN DAYOFWEEK(timestamp) < 6 THEN "Weekday"
    ELSE "Weekend"
  END AS day_type,
  AVG(temperature) AS average_temperature
FROM cleaned_environment
GROUP BY device_id
</pre>

#### Task 14. Calculate the cumulative sum of temperature for each device, ordered by timestamp limit to 10. The objective is to calculate the cumulative sum of temperature for each device, considering the records ordered by timestamp limit to 10.

<pre>
SELECT device_id, timestamp, temperature,
    SUM(temperature) OVER (PARTITION BY device_id ORDER BY timestamp) AS cumulative_temperature
FROM cleaned_environment
LIMIT 10
</pre>
