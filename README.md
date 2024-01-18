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
