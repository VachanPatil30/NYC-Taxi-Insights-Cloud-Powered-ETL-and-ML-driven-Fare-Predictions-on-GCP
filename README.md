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





