# Capstone Project Data Engineering

### Table of Contents

1. [Project Introduction](#intro)
2. [Project Details](#details)
3. [Files](#files)
4. [How To Run](#execution)
4. [Results](#results)
5. [Licensing](#licensing)

## Project Introduction<a name="intro"></a>

This project is part of the Udacity Nanodegree Data Engineer.

The goal and end-usecase behind this project is to offer the analytics team a data basis for their analysis on climate change and the socio-economic effects that come with it. There is temperature data as well as energy consumption data, co2 emission, population numbers and HDI scores. Findings might include for example that climate change in temperature affects countries more than what they have contributed to it.

The idea is to have one facts dataset = Global Land Temperatures by city which should show the climate change in temperature values.
Additionally we will have dimension tables with the following input:
- What is the countries energy consumption?
- What is the countries c02 fossil fuel emission?
- What is the cities population?
- What is the HDI level of the country? The goal is to create a basis for analytics team to start their analysis on climate change and the effects on each nation. There might be statements possible like: "countries who have a low HDI level contributed way less to climate change (co2 emission, energy consumption) than high level HDI countries". So this goes also a bit in the social economic direction as well as seeing who is affected and in what ways.

Insights like these are of interest for the analytics team. The Data Engineers job is to prepare the data according to their requirements, which we will do in this project. Therefore the goal is to offer a solid and useful database that can be queried efficiently for the analytics team to find insights in. The data will be available on AWS Redshift.

## Project Details<a name="details"></a>

The following tools and technologies were used in this project:
- PySpark - to wrangle the raw data
- AWS S3 - to store the cleaned data
- AWS Redshift - to make the tables available for the analytics team

As mentioned we had 5 datasets in total - with 1 facts dataset and 4 dimensional datasets. The following picture shows the final schema of the data:
![Image of table model](./pictures/Data Schema_tables.jpg)

## Files <a name="files"></a>

Here you can see the files of the project with a short description:

```
├── conifg/
    ├── config_redshift.cfg --> config file with all necessary details to create and delete a cluster
    ├── dl.cfg --> config file with IAM Role keys
    ├── dwh.cfg --> config file with all necessary details to run the created cluster on aws
├── data/
    ├── GlobalLandTemperaturesByCity.csv --> temperature data - fact dataset
    ├── HDR21-22_Composite_indices_complete_time_series.csv --> HDI data - dimensional dataset
    ├── population-city.csv --> population data - dimensional dataset
    ├── World Energy Consumption.csv --> energy consumption data - dimensional dataset
├── data_dictionaries/
    ├── co2_emissions.csv --> data dict for co2 emission dataset
    ├── energy_consumption.csv --> data dict for energy consumption dataset
    ├── hdi.csv --> data dict for HDI dataset
    ├── population.csv --> data dict for population dataset
    ├── temperature.csv --> data dict for temperature dataset
├── pictures/
    ├── Data Schema_Tables.jpg --> picture of final data schema tables
    ├── Data Schema.jpg --> picture of data schema dataframes
├── 01_Data_Exploration.ipynb --> jupyter notebook for data exploration
├── 02_Data_Wrangling_ETL.ipynb --> jupyter notebook for data wrangling and storing result in S3
├── 03_create_cluster.py --> python script for creating a Redshift cluster
├── 04_create_tables.ipynb --> jupyter notebook for creating tables in redshift and loading the data from S3
├── 05_delete_cluster.py --> python script for deleting the cluster
├── README.md
```


## How To Run<a name="execution"></a>

- STEP 1: Wrangle the data and write the result to S3 by executing code in 02_Data_Wrangling_ETL.ipynb

- STEP 2:  Create IAM user and note AWS access key and the secret key
  - Create a new IAM user in your AWS account
  - Give it `AdministratorAccess`, From `Attach existing policies directly` Tab
  - Take note of the access key and secret and add both keys into the config_redshift.cfg
  - Additionally fill the following fields with your information:
      - DWH_IAM_ROLE_NAME=...
      - DWH_CLUSTER_IDENTIFIER=...
      - DWH_DB=...
      - DWH_DB_USER=...
      - DWH_DB_PASSWORD=...
      
- STEP 3: Fill out the following information in the dwh.cfg file:
  - DB_NAME=... (=DWH_DB in config_redshift.cfg)
  - DB_USER=... (=DWH_DB_USER in config_redshift.cfg)
  - DB_PASSWORD=... (=DWH_DB_PASSWORD in config_redshift.cfg)

- STEP 4: Start up Redshift cluster by running the following in the Terminal:
  - `python 03_create_cluster.py `

- STEP 5: As instructed by the comments in the Terminal, note down the output to the Endpoint and the Role ARN and write it into the dwh.cfg file:
  - Fill the endpoint in 'host'
  - Fill the role ARN in 'ARN'
  
- STEP 6: Create new tables in redshift cluster and load data into the tables by running 04_create_tables.ipynb
  
- STEP 7: Now you can check the tables using the Query editor in redshift or the data quality checks in the jupyter notebook 04

- STEP 8: After you are done, you can delete the cluster by running the following in the terminal:
  - `python 05_delete_cluster.py`


## Results<a name="results"></a>

This project was a prototype. The data is not big enough to be considered big data - therefore technically Spark would have not been neccessary. Especially because in these jupyter notebook it is running on local mode, and not some bigger cluster (for example EMR). But since this prototype might be the basis for a project with actual big data, it was written in PySpark.

AWS S3 was used for storing the wrangled data. This decision was made because the end tables were supposed to be available in AWS Redshift. The data was copied from S3 to the redshift data warehouse.

The final result are tables in redshift that are ready to be used and queried by the analytics team. The data quality checks ensure that completeness and correctness. 

In the use case of production we would be assuming the following points:

- The data is increased by at least 100 times
- The data was companies data and would be feeded with new data every month (for the protoype it's static)
- The database needs to be accessed by 100+ people

In the scenario where we are dealing with actual big data, the Spark code could of course not run locally anymore. Therefore I would suggest creating an EMR cluster and have it run the script on a certain schedule.

In our case the data is static and is not updated regulary. Since the temperature data is monthly I could imagine that in a production scenario there would also be new data monthly. Therefore it would make sense to have the update schedule also run monthly. This could be done with schedules in EMR directly. Otherwise there are also options like Airflow to schedule a pipeline accordingly - but in this case the project would have to be created a bit differently. Generally it would then make sense to have an update functionality where only the data is processed that is new. Possibly this could be achieved through filtering after month in this case.

If many people need to access a database, redshift is already a great choice. Multiple people can query the data without interfering with eachother.


## Licensing<a name="licensing"></a>

The credit for the data goes to the sources mentioned in 01 and 02 notebook. 

For the code: Feel free to use my code as you please

Copyright 2022

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
