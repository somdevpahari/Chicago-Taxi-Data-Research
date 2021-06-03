# Chicago Taxi Data Research
## Install
This project requires [hive](http://www.apache.org/dyn/closer.cgi/hive/) or [spark](https://www.apache.org/dyn/closer.lua/spark/spark-3.1.2/spark-3.1.2-bin-hadoop3.2.tgz)
## Background

As a big data engineer, you are asked to prepare the data for performing market research on the trends and patterns of Taxi services in Chicago. The report should equip to serve the following target audience:
* Executives and Leadership team from any of the existing taxi service provider or aggregator that operates already in the region.
* Executives and Leadership team from any of the taxi service providers operating in other regions, who are trying to enter the market for the first time.
* Any potential Entrepreneur or investor who is trying to invest in the business of taxi service or aggregator service.

The efforts involved in accumulating primary data is more, and therefore start your market research with secondary data. However, there are cases where secondary data might not be available. Fortunately, in our case, there is a huge dataset (~32 GB) available from Chicago Data Portal that carries information relevant to our market research problem.

## 2. Dataset Introduction
This dataset includes taxi trips from 2013 to 2019, reported to the City of Chicago in its role as a regulatory agency. To protect privacy but allow for aggregate analyses, the Taxi ID is consistent for any given taxi medallion number but does not show the number, Census Tracts are suppressed in some cases, and times are rounded to the nearest 15 minutes. Due to the data reporting process, not all trips are reported but the City believes that most are. Columns present in the dataset are:

Column Name | Description Data | Type
:---------: | :--------------: | :---:
Trip ID     | A unique identifier for the trip. | Text
Taxi ID | A unique identifier for the taxi. | Text 
Trip Start Timestamp | When the trip started, rounded to the nearest 15 minutes. | Floating Timestamp 
Trip End Timestamp | When the trip ended, rounded to the nearest 15 minutes. | Floating Timestamp 
Trip Seconds | Time of the trip in seconds. | Number 
Trip Miles | Distance of the trip in miles. | Number 
Pickup Census Tract | The Census Tract where the trip began. For privacy, this Census Tract is not shown for some trips. This column often will be blank for locations outside Chicago. |Text 
Dropoff Census Tract | The Census Tract where the trip ended. For privacy, this Census Tract is not shown for some trips. This column often will be blank for locations outside Chicago. | Text 
Pickup Community Area | The Community Area where the trip began. This column will be blank for locations outside Chicago. | Number 
Dropoff Community Area | The Community Area where the trip ended. This column will be blank for locations outside Chicago. | Number
Fare | The fare for the trip. | Number 
Tips | The tip for the trip. Cash tips generally will not be recorded. | Number 
Tolls | The tolls for the trip. | Number 
Extras | Extra charges for the trip. | Number 
Trip Total | Total cost of the trip, the total of the previous columns. | Number 
Payment Type | Type of payment for the trip. | Text 
Company | The taxi companies. | Text 
Pickup Centroid Latitude | The latitude of the centre of the pickup census tract or the community area if the census tract has been hidden for privacy. This column often will be blank for locations outside Chicago. | Number
Pickup Centroid Longitude | The longitude of the centre of the pickup census tract or the community area if the census tract has been hidden for privacy. This column often will be blank for locations outside Chicago. | Number 
Pickup Centroid Location | The location of the centre of the pickup census tract or the community area if the census tract has been hidden for privacy. This column often will be blank for locations outside Chicago. | Point 
Dropoff Centroid Latitude | The latitude of the centre of the drop-off census tract or the community area if the census tract has been hidden for privacy. This column often will be blank for locations outside Chicago. | Number 
Dropoff Centroid Longitude | The longitude of the centre of the drop-off census tract or the community area if the census tract has been hidden for privacy. This column often will be blank for locations outside Chicago. | Number 
Dropoff Centroid Location | The location of the centre of the drop-off census tract or the community area if the census tract has been hidden for privacy. This column often will be blank for locations outside Chicago. | Point

Following are some of the key highlights about the dataset:
* It has data about around 71M trips for a duration of 7 years, starting from Jan 2013 to June 2019.
* These trips belong to ~8000 unique taxis identified by a unique identifier.
* There is a trip duration indicating trips ranging from few minutes all the way up to one full day.
* Trip distance ranges from few miles all the way up to ~3500 miles.
* The entire city is divided into 77 different communities, and for each pickup & drop, the community is mentioned in the dataset.
* The information about trip fare is available for each trip.
* Around 30% of the records has the information about the taxi company to which the trip belongs. This data will help you find the market share of these companies.
* The precise pickup location in the form of latitude and longitude is available.

The dataset provides a lot of opportunity to come up with highly relevant insights for the research.

## 3. Problem Definition
The crucial step is to make the dataset ready to perform analyses, so as part of the Data Engineering team, you are responsible to perform,
* Data Cleaning, Loading & Data Pre-processing
* Data Preparation
in order to apply the following techniques:
* Forecasting: Possibly to find out the growth of market size at various granularity levels.
* Clustering: Applying at various dimensions like pickup, drop off communities, different time periods to find out interesting patterns.
* Classification: Labelling different time periods as peak hour, low activity, etc. and applying classification mechanisms on top of the same.
* Graph analysis: Creating origin-destination pairs based on the 77 communities and apply graph analysis such as connected components.

While above mentioned techniques can be applied on top of the data, each of them requires the data to be prepared in a specific format.

## 3.1 Cleaning, Loading and Pre-processing Data
The Dataset that is available publicly is not in a proper format to be directly used for data preparation. So, we cleaned and transformed it in the pre-processing stage.

## 3.2 Data Preparation
## 3.2.1 Data Summary
First step in data processing is to create a detailed summary of the data on various dimensions. In order to create data summary, write hive queries with detailed explanation for the following questions:

1. What are the total number of trips per year?
2. Create the same summary for number of trips at monthly level.
3. Calculate the percentage of records that contains drop-off community value. Excluding all the NULL records, find out the top 10 communities, where people travel to, based on the drop-off community field and also find its percentage to the total number of trips.
4. Create a table which contains the total number of trips for each drop-off community across each year. Using the above table, find the top 10 records based on number of trips with year and drop_off community. Remove the null record while creating the table to remove inconsistencies.
5. Create a table which contains total number of trips for each drop-off communities across weekdays & weekends to check if there is any sort of pattern visible. After creating the table, find the top 10 drop off communities based on number of trips where people travel on weekdays. Find the same for the weekends. Also find the total number of trips taken on weekdays & weekends and their ratio.
6. Find the distribution of total number of trips based on trip duration, like <1 hr, 1 to 2 hr, 2 to 3, … 22 to 23 hr. Note that this requires converting trip_seconds into trip_hours as pre-processing. Remove the trips that do not contain trip duration.
7. Find the top 10 buckets of the number of trips distribution based on the distance covered. Also round off the trip miles to the nearest integer. Remove the trips that do not contain distance.
8. Find top 10 buckets of the number of trips distribution based on the trip fare. Also round off the trip fare to the nearest integer. Remove the trips that do not contain trip fare.
9. Compute the average trip fare per day. Also compute the average trip fare per trip. Compute the same based on weekdays and weekend days. Find out if there is any substantial difference observed.
10. Create a table to store the taxi wise total fare & total number of trips for each day. Find the following insights from the table:
    1. Find the top 10 taxis based on average trips per day.
    2. Find the top 10 taxis based on average fare per day

## 3.2.2 Data Preparation for Forecasting
Forecasting refers to predicting what will happen in the future by taking into consideration the events in the past and present. Basically, it is a technique that helps businesses cope with the impact of the future’s uncertainty by using historical data and trends. This technique can be applied at a daily, weekly and monthly levels depending on the amount of data available. Your task is to create all the tables. Following is the format in which the data needs to be prepared for applying forecasting.
### 1. Daily Summary table
Date | Day of week | Month | Year | Weekend/ Weekday | Total Trip count | Total Trip fare | Total trip miles Total trip duration (min) | Avg Trip fare | Avg Trip miles | Avg trip duration (min)
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---

### 2. Weekly Summary table
Week_No | Date_From | Date_To | Month | Total Trip count | Total Trip fare | Total trip miles | Total trip duration (min) | Avg Trip fare | Avg Trip miles | Avg trip duration(min)
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---
### 3. Monthly Summary table
Month_no | Date_From | Date_To | Year | Total Trip count | Total Trip fare | Total trip miles | Total trip duration (min) | Avg Trip fare | Avg Trip miles | Avg trip duration(min)
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---

## 3.2.3 Data Preparation for Community Summary

There are couple of attributes called pickup_community and dropoff_community available in the dataset. They indicate the community area of the pickup and drop off location of the trip respectively. In order to find insights related to community, you need to prepare the data in the below format for pickup & dropoff communities.

Pickup Community ID | Daily Trip Count | Daily Total Fare | Daily Total Distance | Daily Total Duration | Daily Average Amount | Daily Average Distance | Daily Average Duration
--- | --- | --- | --- | --- | --- | --- | --- 

Dropoff Community ID | Daily Trip Count | Daily Total Fare | Daily Total Distance | Daily Total Duration | Daily Average Amount | Daily Average Distance | Daily Average Duration
--- | --- | --- | --- | --- | --- | --- | ---

## 3.2.4 Data Preparation for Origin to Destination Pair Summary
Another interesting analysis is something that can be performed at an Origin-Destination pair level. Ideally there are 2926 bidirectional and 5852 unidirectional connections/edges possible from the 77 communities. But not all the connections may have data. For example, between two given communities, there may not be any trips in the past.

The summary table has to be in the following format.

Community_orig | Community_dest | Trip_count | Total_trip_miles | Avg_trip_miles | Avg_trip_duration | Avg_trip_fare
--- | --- | --- | --- | --- | --- | ---

## 3.2.5 Data Preparation for Company Summary
Only 30% of the trips have the taxi company detail filled in. Extract the available company name/detail and create a separate company detail table.

Create a summary table at a company level with the following details:

Company | Daily Trip Count | Daily Total Fare | Daily Total Distance | Daily Total Duration | Daily Average Amount | Daily Average Distance | Daily Average Duration
--- | --- | --- | --- | --- | --- | --- | ---

## 3.2.6 Data Summary in RDBMS
Some of the data summary generated at company and community level can be stored in an RDBMS (like MySQL) for quick querying and inspection. It is also ideal to connect any reporting or BI tools to MySQL instead of trying to connect to Hive tables.

Following three are the potential candidates for storing in MySQL and serve.

Pickup Community ID | Daily Trip Count | Daily Total Fare | Daily Total Distance | Daily Total Duration | Daily Average Amount | Daily Average Distance | Daily Average Duration
--- | --- | --- | --- | --- | --- | --- | ---

Dropoff Community ID | Daily Trip Count | Daily Total Fare | Daily Total Distance | Daily Total Duration | Daily Average Amount | Daily Average Distance | Daily Average Duration
--- | --- | --- | --- | --- | --- | --- | ---

Company | Daily Trip Count | Daily Total Fare | Daily Total Distance | Daily Total Duration | Daily Average Amount | Daily Average Distance | Daily Average Duration
--- | --- | --- | --- | --- | --- | --- | ---

## 3.2.7 Summary Data Mart

While the resulting summary can be stored into Hive in the form of various tables, it makes sense to store a detailed summary data mart in HBase or Cassandra. So that it is easier to,
* Search for entities (companies, communities, etc.) that match specific search criteria.
* To perform aggregations on selected set of dimensions, like revenue, trip count, etc.

Create an HBase table in the above format. Write an HBase Java API to insert the data.

Data stored into these data marts can be queried for some complicated search use cases which will be hard to fit into MySQL. Any potential APIs to serve the required results with these search use cases can be developed on top of this data mart.



































