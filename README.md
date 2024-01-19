# NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP

## Objective
In this project, my objective was to design and implement an end-to-end data pipeline, encompassing the following stages:

1. Extracted 100k records from the 2016 TLC Website, loading them into Google Cloud Storage for initial processing.
2. Applied fact and dimensional data modeling techniques using Python on Jupyter Notebook to comprehensively transform and model the data.
3. Orchestrated the ETL pipeline on Mage AI, ensuring the seamless loading of transformed data into Google BigQuery.
4. Conducted in-depth analysis using BigQuery, uncovering key patterns translated into a user-friendly Looker Studio dashboard.
5. Implemented a precise taxi fare forecasting model using BQML, achieving an RMSE of 4.03 for accurate prediction.

## Dataset Used
In our project we used a dataset from TLC Trip Record Data Yellow and green taxi trip records include fields capturing pick-up and drop-off dates/times, pick-up and drop-off locations, trip distances, itemized fares, rate types, payment types, and driver-reported passenger counts.

Here is the dataset used in the project: https://storage.googleapis.com/nyc_taxi_data_engineering/NYC_Taxi.csv

More info about the dataset can be found from the following links:
- Website: https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page       
- Data Dictionary: https://www.nyc.gov/assets/tlc/downloads/pdf/data_dictionary_trip_records_yellow.pdf

## Technology Used
1. Programming Language:- Python, SQL
2. Google Cloud Platform:- 
   - Google Storage
   - Compute Instance
   - BigQuery
   - Looker Studio Dashboard:- https://lookerstudio.google.com/reporting/079bed3f-5364-400c-8e27-444739601af5
   - BQML
3. Modern Data Pipeine Tool:- https://www.mage.ai/
4. Lucidchart:- https://www.lucidchart.com/pages/

## Data Modeling
Created the ER diagram using Lucidchart, facilitating the visualization of how we intended to transform the flat table into a fact table

![NYC_Taxi_Data_Model](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/d21039de-4971-40ea-8f70-51ea6637fd37)

## Step 1: Cleaning and Transformation
In this step, I loaded the CSV file into Google Colab and carried out data cleaning and transformation activities before organizing them into fact and dim tables.

Here are the specific cleaning and transformation tasks that were performed:
* Converted tpep_pickup_datetime and tpep_dropoff_datetime columns into datetime format.
* Removed duplicates and reset the index.
* We transformed the flat file into fact and dimension tables, following the principles of data modeling.

[Nyc_Taxi_Analytics.ipynb](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/blob/48a34fbdcec8d473efd0116359a74a61fc8522fd/Nyc_Taxi_Analytics.ipynb)

## Step 2: ETL / Orchestration using mage
1. Start by launching the SSH instance and installing the necessary libraries with the provided commands.
   
```python
# Install python and pip 
sudo apt-get install update

sudo apt-get install python3-distutils

sudo apt-get install python3-apt

sudo apt-get install wget

wget https://bootstrap.pypa.io/get-pip.py

sudo python3 get-pip.py

# Install Google Cloud Library
sudo pip3 install google-cloud

sudo pip3 install google-cloud-bigquery

# Install Pandas
sudo pip3 install pandas
```
2. Install the Mage AI library from the Mage AI GitHub and create a new project named "NYC Taxi Data Engineering "
```python 
# Install Mage library
sudo pip3 install mage-ai

# Create new project
mage start demo_project
```
3. Perform orchestration in Mage by accessing the external IP address in a new tab using the format: <external IP address>:<port number>.
4. Create a new pipeline with the following stages:
   * Extract: [nyc_taxi_load](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/blob/d9323960005c18e7a90ba20f88fa8056a6d92875/Mage/nyc_taxi_loader.py)
   * Transform: [nyc_taxi_transformer](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/blob/d9323960005c18e7a90ba20f88fa8056a6d92875/Mage/nyc_taxi_transformer.py)
   * Load: [nyc_taxi_bigquery](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/blob/d9323960005c18e7a90ba20f88fa8056a6d92875/Mage/nyc_taxi_bigquery.py)

Before executing the Load pipeline, download credentials from Google API & Credentials. Update these credentials in the io_config.yaml file within the same pipeline. This step is crucial for authorizing access and loading data into Google BigQuery.

## Step 3: Clean and Transform Data in BigQuery
In this section, I will discuss how I cleaned and prepared the data after pipelining the data from mage to big query

### 1. Creating the Analytics Table by Joining Tables
To consolidate and prepare the data, I created a final analytics table by joining various tables. This SQL script performs the necessary joins and selects relevant columns to form the analytics_table.
```python
CREATE OR REPLACE TABLE `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table` AS (
SELECT 
  f.trip_id,
  f.VendorID,
  d.tpep_pickup_datetime,
  d.tpep_dropoff_datetime,
  p.passenger_count,
  t.trip_distance,
  r.rate_code_name,
  pick.pickup_latitude,
  pick.pickup_longitude,
  drop.dropoff_latitude,
  drop.dropoff_longitude,
  pay.payment_type_name,
  f.fare_amount,
  f.extra,
  f.mta_tax,
  f.tip_amount,
  f.tolls_amount,
  f.improvement_surcharge,
  f.total_amount
FROM `nyc-taxi-data-engineering.nyc_taxi_dataset.fact_table` AS f
INNER JOIN `nyc-taxi-data-engineering.nyc_taxi_dataset.datetime_dim` AS d  
  ON f.datetime_id = d.datetime_id
INNER JOIN `nyc-taxi-data-engineering.nyc_taxi_dataset.passenger_count_dim` AS p
  ON p.passenger_count_id = f.passenger_count_id  
INNER JOIN `nyc-taxi-data-engineering.nyc_taxi_dataset.trip_distance_dim` AS t
  ON t.trip_distance_id = f.trip_distance_id  
INNER JOIN `nyc-taxi-data-engineering.nyc_taxi_dataset.rate_code_dim` AS r 
  ON r.rate_code_id = f.rate_code_id  
INNER JOIN `nyc-taxi-data-engineering.nyc_taxi_dataset.pickup_location_dim` AS pick
 ON pick.pickup_location_id = f.pickup_location_id
INNER JOIN `nyc-taxi-data-engineering.nyc_taxi_dataset.dropoff_location_dim` AS drop
  ON drop.dropoff_location_id = f.dropoff_location_id
INNER JOIN `nyc-taxi-data-engineering.nyc_taxi_dataset.payment_type_dim` AS pay
  ON pay.payment_type_id = f.payment_type_id
);
```

### 2. Removing Negative Total Amounts
Negative total amounts are invalid, so I filtered the records to exclude them while keeping those with a total amount of 0 (indicating canceled rides).
```python
#Removing the negative total amount
CREATE OR REPLACE TABLE `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table` AS(
SELECT *
FROM `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`
WHERE total_amount>=0);
```

### 3. Removing rows with invalid latitude or longitude
To ensure data accuracy, I filtered out records with invalid latitude or longitude values (0 values) for both pickup and dropoff locations.
```python
#Removing rows with invalid latitude or longitude
CREATE OR REPLACE TABLE `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table` AS(
select *
from `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`
WHERE (pickup_latitude != 0 AND pickup_longitude != 0) 
  AND
  (dropoff_latitude != 0 AND dropoff_longitude != 0))
```

### 4. Adding zones and boroughs
Utilizing the public dataset new_york_taxi_trips.taxi_zone_geom in BigQuery, I enriched the analytics table by incorporating pickup and dropoff zones and boroughs.
```python
# Adding zones and boroughs
CREATE OR REPLACE TABLE `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table` AS (
  SELECT 
    t.*,
    tz_pu.zone_id AS pickup_zone_id,
    tz_pu.zone_name AS pickup_zone_name,
    tz_pu.borough AS pickup_borough,
    tz_do.zone_id AS dropoff_zone_id,
    tz_do.zone_name AS dropoff_zone_name,
    tz_do.borough AS dropoff_borough,
    CONCAT(tz_pu.borough, "-", tz_do.borough) AS route_borough,
    CONCAT(tz_pu.zone_name, "-", tz_do.zone_name) AS route_zone_name
FROM 
  `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table` t
/* find the boroughs and zone names for dropoff locations */
INNER JOIN `bigquery-public-data.new_york_taxi_trips.taxi_zone_geom` tz_do ON 
  ST_DWithin(tz_do.zone_geom, ST_GeogPoint(t.dropoff_longitude, t.dropoff_latitude), 0)
/* find the boroughs and zone names for pickup locations */
INNER JOIN `bigquery-public-data.new_york_taxi_trips.taxi_zone_geom` tz_pu ON 
  ST_DWithin(tz_pu.zone_geom, ST_GeogPoint(t.pickup_longitude, t.pickup_latitude), 0));
```

## Step 4: Analytics
After cleaning and transforming our data we will perform some analysis on them:

### 1. Timeframe Covered by Yellow Taxi Trips
```python
# What is the timeframe covered by the Yellow taxi trips in our dataset?
SELECT
 min(tpep_pickup_datetime) as start_date, max(tpep_dropoff_datetime) as end_date
FROM
  `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`
```
![Screenshot 2024-01-18 222920](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/931a4418-c034-45ea-b61b-6ffe02b3284e)                              

The dataset covers taxi records from March 1 to March 11, 2016.

 ### 2. Average Speed of Yellow Taxi Trips
 ```python
SELECT CONCAT(ROUND(AVG(trip_distance/TIMESTAMP_DIFF(tpep_dropoff_datetime, tpep_pickup_datetime, SECOND)*3600),2)," MPH") as AVG_Speed
FROM  `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`
WHERE trip_distance>0
AND tpep_dropoff_datetime>tpep_pickup_datetime
```
![Screenshot 2024-01-18 223248](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/11370256-83b8-4387-8120-bf0cc7940f8c)

The average speed of Yellow taxis is calculated to be 12.76 MPH.

### 3. Trip Cancellation Rate
```python
SELECT 
  COUNTIF(trip_distance = 0) as trip_cancelled,
  COUNT(*) as total_trips,
  CONCAT(ROUND(COUNTIF(trip_distance = 0) / COUNT(*)*100,2),'%') as cancellation_rate
FROM `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`;
```
![Screenshot 2024-01-18 223502](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/2e18ca63-72f1-4967-935a-0a472df51a03)

The dataset recorded 98,623 trips, with 332 cancellations, resulting in a cancellation rate of 0.34%.

### 4. Top 3 Popular Pickup Locations
```
SELECT pickup_borough as Borough, COUNT(*) as total_trips,
concat(ROUND (COUNT(*)/(SELECT COUNT(*) FROM `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`)*100, 2),"%") AS Trip_rate
FROM `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`
GROUP BY pickup_borough
ORDER BY 2 DESC
LIMIT 3
```
![Screenshot 2024-01-18 223922](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/559972bd-26e1-405b-8800-b290b2019fa7)

Manhattan stands out as the most popular pickup location, constituting 92% of trips.

### 5. Popular routes
```python
SELECT route_borough as Route, COUNT(*) as total_trips,
concat(ROUND (COUNT(*)/(SELECT COUNT(*) FROM `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`)*100, 2),"%") AS Trip_rate
FROM `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`
GROUP BY route_borough
ORDER BY 2 DESC
```
![Screenshot 2024-01-18 224423](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/8f035dc6-6f04-4a17-9d02-713726bed84b)

About 83% of the popular routes are within Manhattan, providing insights into passenger preferences.

### 6. Top 5 Routes by zone
```python
SELECT route_zone_name as Route, COUNT(*) as total_trips,
concat(ROUND (COUNT(*)/(SELECT COUNT(*) FROM `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`)*100, 2),"%") AS Trip_rate,
CONCAT(ROUND(AVG(trip_distance),2)," Miles") as Avg_distance
FROM `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`
GROUP BY route_zone_name
ORDER BY 2 DESC
LIMIT 5
```
![Screenshot 2024-01-18 224736](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/c7108d92-ecf2-4a29-af4e-c007f7c61699)

Identifying popular routes by zone provides valuable information for directing drivers and estimating trip distances.

### 7. Payment Type Distribution
```python
SELECT payment_type_name, COUNT(*) as Total_trips
FROM `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`
GROUP BY 1
order by 2 DESC
```
![Screenshot 2024-01-18 225415](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/140632d1-c189-44d6-a2b0-e1fc2e7a7872)

The majority of payments are made by credit card, emphasizing the importance of a reliable payment gateway. Payments in cash are observed, and instances recorded as "No charge" or "Dispute" signal potential issues that warrant investigation by the Customer Satisfaction Department for resolution and improved service.

## Step 5: Dashboard
Following the analysis, I imported pertinent tables into Looker Studio and crafted a dashboard, accessible for viewing [here](https://lookerstudio.google.com/reporting/079bed3f-5364-400c-8e27-444739601af5).

## Step 6: Predict Taxi Fare with a BigQuery ML Forecasting Model
**Objective:** Create a machine learning model in BigQuery to predict New York City cab ride fares using historical trip data. Predicting fares in advance enhances trip planning for both riders and taxi agencies.

Here we will perform the following tasks:
1. Query and explore our dataset for the predictive analysis.
2. Create a training and evaluation dataset to be used for batch prediction.
3. Create a forecasting (linear regression) model in BQML.
4. Evaluate the performance of your machine learning model.

### Task 1: Explore Dataset and Analyze Average Speed
 ```python
SELECT
  EXTRACT(HOUR
  FROM
    tpep_pickup_datetime) hour,
  ROUND(AVG(trip_distance / TIMESTAMP_DIFF(tpep_dropoff_datetime,
        tpep_pickup_datetime,
        SECOND))*3600, 1) speed
FROM
  `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`
WHERE
  trip_distance > 0
  AND tpep_dropoff_datetime > tpep_pickup_datetime
GROUP BY
  1
ORDER BY
  1
```
![Screenshot 2024-01-18 233813](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/395dd57f-e4fb-46ae-86db-892464f26bd4)

During the day, the average speed is around 9-10 MPH; however, at 5:00 AM, the average speed almost triples to 30 MPH, likely due to reduced traffic.

### Task 2: Select features and create your training dataset
Select relevant features for training the machine learning model. Create a training dataset by filtering and extracting necessary fields.
```python
#Select features and create your training dataset

WITH params AS (
    SELECT
    1 AS TRAIN,
    2 AS EVAL
    ),

  daynames AS
    (SELECT ['Sun', 'Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat'] AS daysofweek),

  taxitrips AS (
  SELECT
    (tolls_amount+ tip_amount+ fare_amount) AS total_amount,
    daysofweek[ORDINAL(EXTRACT(DAYOFWEEK FROM tpep_pickup_datetime))] AS dayofweek,
    EXTRACT(HOUR FROM tpep_pickup_datetime) AS hourofday,
    pickup_longitude AS pickuplon,
    pickup_latitude AS pickuplat,
    dropoff_longitude AS dropofflon,
    dropoff_latitude AS dropofflat,
    passenger_count AS passengers,
    trip_distance AS Distance
    
  FROM
    `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`, daynames, params
  WHERE
    trip_distance > 0 AND fare_amount > 0
    AND MOD(ABS(FARM_FINGERPRINT(CAST(tpep_pickup_datetime AS STRING))),1000) = params.TRAIN
  )

  SELECT *
  FROM taxitrips
```
After Browsing the complete list of fields and then previewing the dataset to find useful features that will help a machine learning model understand the relationship between data about historical cab rides and the price of the fare.
We decided to go with the below fields are good inputs to your fare forecasting model:
- Tolls Amount
- Fare Amount
- Tip Amount
- Hour of Day
- Pick up address
- Drop off address
- Number of passengers
- Trip Distance

There are a few things about the query to note:
- The main part of the query is at the bottom (SELECT * from taxitrips).
- taxitrips does the bulk of the extraction for the NYC dataset, with the SELECT containing your training features and label.
- The WHERE removes data that you don't want to train on. We have removed the records which had trip distance as 0 and fare amount as 0 since it would disturb our prediction.
- The WHERE also includes a sampling clause to pick up only 1/1000th of the data.
- Define a variable called TRAIN so that you can quickly build an independent EVAL set. 

### Task 3: Create a BigQuery dataset to store models
Create a new BigQuery dataset to train and store machine learning models.
```python
# Create BigQuery ML model
CREATE or REPLACE MODEL `nyc-taxi-data-engineering.nyc_taxi_dataset.taxifare_model`
OPTIONS
  (model_type='linear_reg', labels=['total_fare']) AS

WITH params AS (
    SELECT
    1 AS TRAIN,
    2 AS EVAL
    ),

  daynames AS
    (SELECT ['Sun', 'Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat'] AS daysofweek),

  taxitrips AS (
  SELECT
    (tolls_amount+ tip_amount + fare_amount) AS total_fare,
    daysofweek[ORDINAL(EXTRACT(DAYOFWEEK FROM tpep_pickup_datetime))] AS dayofweek,
    EXTRACT(HOUR FROM tpep_pickup_datetime) AS hourofday,
    pickup_longitude AS pickuplon,
    pickup_latitude AS pickuplat,
    dropoff_longitude AS dropofflon,
    dropoff_latitude AS dropofflat,
    passenger_count AS passengers,
    trip_distance AS Distance

  FROM
    `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`, daynames, params
  WHERE
    trip_distance > 0 AND fare_amount > 0
    AND MOD(ABS(FARM_FINGERPRINT(CAST(tpep_pickup_datetime AS STRING))),1000) = params.TRAIN
  )

  SELECT *
  FROM taxitrips
```

### Task 4: Evaluate classification model performance
For linear regression models we should use a loss metric like Root Mean Square Error (RMSE). We want to keep training and improving the model until it has the lowest RMSE.
In BQML, mean_squared_error is a queryable field when evaluating your trained ML model. Add a SQRT() to get RMSE. Now that training is complete, you can evaluate how well the model performs with this query using ML.EVALUATE.
```# Evaluate model performance
SELECT
  SQRT(mean_squared_error) AS rmse
FROM
  ML.EVALUATE(MODEL nyc_taxi_dataset.taxifare_model,
  (

  WITH params AS (
    SELECT
    1 AS TRAIN,
    2 AS EVAL
    ),

  daynames AS
    (SELECT ['Sun', 'Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat'] AS daysofweek),

  taxitrips AS (
  SELECT
    (tolls_amount+ tip_amount + fare_amount) AS total_fare,
    daysofweek[ORDINAL(EXTRACT(DAYOFWEEK FROM tpep_pickup_datetime))] AS dayofweek,
    EXTRACT(HOUR FROM tpep_pickup_datetime) AS hourofday,
    pickup_longitude AS pickuplon,
    pickup_latitude AS pickuplat,
    dropoff_longitude AS dropofflon,
    dropoff_latitude AS dropofflat,
    passenger_count AS passengers,
    trip_distance AS Distance
  FROM
    `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`, daynames, params
  WHERE
    trip_distance > 0 AND fare_amount > 0
    AND MOD(ABS(FARM_FINGERPRINT(CAST(tpep_pickup_datetime AS STRING))),1000) = params.EVAL
  )

  SELECT *
  FROM taxitrips

  ))
```
![Screenshot 2024-01-18 235516](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/99910e42-5930-4047-a4ac-a1e462e74043)

The RMSE is 4.35, indicating a good model fit with an error of approximately +-$4.35.

### Task 5: Predict taxi total amount
Use the trained model to predict taxi fares alongside actual fares and other features.
```python
# Predict taxi amount
SELECT
*
FROM
  ml.PREDICT(MODEL `nyc-taxi-data-engineering.nyc_taxi_dataset.taxifare_model`,
   (

 WITH params AS (
    SELECT
    1 AS TRAIN,
    2 AS EVAL
    ),

  daynames AS
    (SELECT ['Sun', 'Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat'] AS daysofweek),

  taxitrips AS (
  SELECT
    (tolls_amount+ tip_amount + fare_amount) AS total_fare,
    daysofweek[ORDINAL(EXTRACT(DAYOFWEEK FROM tpep_pickup_datetime))] AS dayofweek,
    EXTRACT(HOUR FROM tpep_pickup_datetime) AS hourofday,
    pickup_longitude AS pickuplon,
    pickup_latitude AS pickuplat,
    dropoff_longitude AS dropofflon,
    dropoff_latitude AS dropofflat,
    passenger_count AS passengers,
    trip_distance AS Distance
  FROM
    `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`, daynames, params
  WHERE
    trip_distance > 0 AND fare_amount > 0
    AND MOD(ABS(FARM_FINGERPRINT(CAST(tpep_pickup_datetime AS STRING))),1000) = params.EVAL
  )

  SELECT *
  FROM taxitrips

));
```
Now we will see the model's predictions for taxi fares alongside the actual fares and other features for those rides. Results should resemble the example provided.:
![Screenshot 2024-01-19 000056](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/ad7603a9-2543-4e3e-a5d8-5fabe0463403)

### Task 6: Improving the model with Feature Engineering by checking statistics of the pasrameter
Iteratively enhance the model by exploring statistics of parameters. In this example, observe statistics related to fare, distance, and passengers.
```python
#Improving the model with Feature Engineering
SELECT
  COUNT(fare_amount) AS num_fares,
  MIN(fare_amount) AS low_fare,
  MAX(fare_amount) AS high_fare,
  AVG(fare_amount) AS avg_fare,
  STDDEV(fare_amount) AS stddev_fare,
  COUNT(trip_distance) AS num_fares,
  MIN(trip_distance) AS low_distance,
  MAX(trip_distance) AS high_distance,
  AVG(trip_distance) AS avg_distance,
  STDDEV(trip_distance) AS stdev_distance,
  COUNT(passenger_count) AS num_passengers,
  MIN(passenger_count) AS low_passengers,
  MAX(passenger_count) AS high_passengers,
  AVG(passenger_count) AS avg_passengers,
  STDDEV(passenger_count) AS stdev_passenger
FROM
`nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`
```
![Screenshot 2024-01-19 000409](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/53f87c3d-9d00-45f8-a23b-fc59d1e91571)

Based on the observation, passenger count standard deviation is low (1.6). Removing it for the second model could improve performance.

### Task 7: Create 2nd BigQuery ML model and training it
Create a second model by removing passenger count and limiting latitude and longitude within the proper NYC range.
```python
CREATE OR REPLACE MODEL `nyc-taxi-data-engineering.nyc_taxi_dataset.taxifare_model_2`
OPTIONS
  (model_type='linear_reg', labels=['total_fare']) AS


WITH params AS (
    SELECT
    1 AS TRAIN,
    2 AS EVAL
    ),

  daynames AS
    (SELECT ['Sun', 'Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat'] AS daysofweek),

  taxitrips AS (
  SELECT
    (tolls_amount + tip_amount + fare_amount) AS total_fare,
    daysofweek[ORDINAL(EXTRACT(DAYOFWEEK FROM tpep_pickup_datetime))] AS dayofweek,
    EXTRACT(HOUR FROM tpep_pickup_datetime) AS hourofday,
    SQRT(POW((pickup_longitude - dropoff_longitude),2) + POW(( pickup_latitude - dropoff_latitude), 2)) as dist, #Euclidean distance between pickup and drop off
    SQRT(POW((pickup_longitude - dropoff_longitude),2)) as longitude, #Euclidean distance between pickup and drop off in longitude
    SQRT(POW((pickup_latitude - dropoff_latitude), 2)) as latitude, #Euclidean distance between pickup and drop off in latitude
    trip_distance AS Distance
    
  FROM
    `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`, daynames, params
WHERE trip_distance > 0 
    AND pickup_longitude > -75 #limiting of the distance the taxis travel out
    AND pickup_longitude < -73
    AND dropoff_longitude > -75
    AND dropoff_longitude < -73
    AND pickup_latitude > 40
    AND pickup_latitude < 42
    AND dropoff_latitude > 40
    AND dropoff_latitude < 42
    AND MOD(ABS(FARM_FINGERPRINT(CAST(tpep_pickup_datetime AS STRING))),1000) = params.TRAIN
  )

  SELECT *
  FROM taxitrips
```

### Task 8: Evaluate the new model
Evaluate the performance of the optimized model with improved features and without passenger count.
```python
# Evaluate 2nd model performance
SELECT
  SQRT(mean_squared_error) AS rmse
FROM
  ML.EVALUATE(MODEL nyc_taxi_dataset.taxifare_model_2,
  (

  WITH params AS (
    SELECT
    1 AS TRAIN,
    2 AS EVAL
    ),

  daynames AS
    (SELECT ['Sun', 'Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat'] AS daysofweek),

  taxitrips AS (
  SELECT
    (tolls_amount + fare_amount) AS total_fare,
    daysofweek[ORDINAL(EXTRACT(DAYOFWEEK FROM tpep_pickup_datetime))] AS dayofweek,
    EXTRACT(HOUR FROM tpep_pickup_datetime) AS hourofday,
    SQRT(POW((pickup_longitude - dropoff_longitude),2) + POW(( pickup_latitude - dropoff_latitude), 2)) as dist, #Euclidean distance between pickup and drop off
    SQRT(POW((pickup_longitude - dropoff_longitude),2)) as longitude, #Euclidean distance between pickup and drop off in longitude
    SQRT(POW((pickup_latitude - dropoff_latitude), 2)) as latitude, #Euclidean distance between pickup and drop off in latitude
    trip_distance AS Distance
  FROM
    `nyc-taxi-data-engineering.nyc_taxi_dataset.analytics_table`, daynames, params
WHERE trip_distance > 0 
    AND pickup_longitude > -75 #limiting of the distance the taxis travel out
    AND pickup_longitude < -73
    AND dropoff_longitude > -75
    AND dropoff_longitude < -73
    AND pickup_latitude > 40
    AND pickup_latitude < 42
    AND dropoff_latitude > 40
    AND dropoff_latitude < 42
    AND MOD(ABS(FARM_FINGERPRINT(CAST(tpep_pickup_datetime AS STRING))),1000) = params.EVAL
  )

  SELECT *
  FROM taxitrips

  ))
```
![Screenshot 2024-01-19 001400](https://github.com/VachanPatil30/NYC-Taxi-Insights-Cloud-Powered-ETL-and-ML-driven-Fare-Predictions-on-GCP/assets/79377852/3b180a49-a671-43b9-b94e-481a23c45136)

The evaluation results indicate a significant improvement in the model's performance, with the Root Mean Squared Error (RMSE) reduced from 4.35 to 4.03. This reduction in RMSE suggests enhanced accuracy in predicting NYC taxi fares.
