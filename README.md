# U.S. Climatological data analysis in Google Cloud Platform

## Overview

The role of weather data is critical in numerous applications, including predicting weather patterns, studying climate change, and understanding local weather conditions. We have focused on the U.S. Local Climatological Data obtained at a station level from the National Oceanic and Atmospheric Administration (NOAA). To understand the trend of weather patterns over time across the US. Identify the zones/clusters of regions where climate change is more prominent than others. Also, help identity PG&E to address the surge in demand ahead of time in a data-driven manner which inturn reduces stress on the power grid in peak power consumption periods like winter and snow.

# Data Overview

NOAA (National Oceanic and Atmospheric Administration) is the primary data source for weather information and we’re worked with 3 key datasets
Historical weather data from NOAA’s FTP server (https://www.ncei.noaa.gov/pub/data/ghcn/daily/by_year/) - this contains data for weather stations from 180 countries but we have filtered for USA only. We have considered data from 2020 to 2023 and this data altogether amounts to ~1.5 GB

Real time weather data from NOAA’s web services API (https://www.ncdc.noaa.gov/cdo-web/webservices/v2)

Weather station mapping for California stations (FIPS:06) from NOAA’s web services API

We worked with 3 weather metrics Temperature (TMIN, TMAX, TAVG), Precipitation (PRCP) and Snowfall (SNOW) captured for each day for each weather station.

# Scheduling using Apache Airflow

In our project, the ETL (Extract, Transform, Load) pipeline is coordinated using Apache Airflow. Utilizing the Directed Acyclic Graph (DAG) script architecture provided by Airflow, we have orchestrated the flow of data across specific tasks defined by us. By defining tasks and dependencies in DAG, it ensures that tasks are run in the correct order and at the right time. These Python-written DAG scripts ensure efficient execution for both real-time and archived data sources.

# Statistics and Machine Learning 

Using the massive amount of weather data at our disposal, we were interested in 3 key data science use cases: 

 Use case 1: Difference between temperature over the years

 To do this, we first considered the median of TAVG (average temperature) across all stations for each day of the year for 2021 and 2022. We hypothesized that there would be a significant difference between these 2 years’ temperatures. To test this, we ran a t-test with our null hypothesis being “There is no significant difference between the temperatures of 2021 and 2022”, hence our alternative hypothesis becomes “There is a significant difference between the temperatures of 2021 and 2022”. The outcome was that the p-value for the independent t-test was lower than the critical value i.e. 0.05, indicating that we have sufficient evidence to reject the null hypothesis and conclude that there is a significant difference between 2021 and 2022 temperatures. 

 Use case 2: Distribution of temperature

We wanted to understand if median daily temperature across 2022 follows a normal distribution or not. To prove or disprove this mathematically, we used a Shapiro-Wilk test. In the Shapiro-Wilk test, null hypothesis = Sample is from the normal distributions. (p-value>0.05). Hence, if we obtain a p-value > 0.05, we accept the null hypothesis and can conclude that the data is normally distributed. FRom histogram visual inspection of the data, we see a somewhat bimodal distribution of data and this doesn't look like a bell curve. We can confirm our suspicion by running the Shapiro-Wilk test.The p-value <0.05 implies that we reject the null hypothesis and can conclude that the data is NOT normally distributed.


 Use case 3: Extreme weather data clustering for California stations

 California is one of the most geographically diverse states in the US. Despite being a coastal state, there are several dry-desert-like areas but also snowy mountains and picturesque beaches. Hence, for such a unique region, utility companies like PG&E (California’s largest utilities provider) need to understand weather patterns to be prepared for seamless power supply even in extreme conditions. To do this, we propose a novel approach of using k-means clustering to identify clusters or groups of stations that have similar precipitation and snowfall conditions. For this, we considered 756 weather stations in the state of California for which we had good coverage of precipitation (total precipitation for the year 2022) and snowfall (average yearly snowfall) data. With this, we ran a kmeans clustering algorithm to understand the groups of stations that showed similar weather patterns.

After the data was aggregating 2022 data for 756 stations (sum of precipitation and average of snowfall), we first checked if these are correlated. Since the correlation was quite low, we can proceed with the next step of scaling the data. K-means is an unsupervised machine learning algorithm wherein we can cluster datapoints that have similar characteristics. Since we don’t know how many clusters we will get at the end, we first need to iterate through different values of k and find the optimal value - this is done through a method called the elbow curve, wherein we plot the different values of k against the sum of squared distances (SSD) between each point from the respective cluster centroids. Based on analyzing the clusters, we can observe that there each station can show 1 among 5 characteristics:
        a.	Low precipitation with no snow - dry habitable regions
        b.	High precipitation and snow - extreme regions
        c.	Very high precipitation with some snow - wet regions
        d.	Average precipitation and high snow - snowy regions
        e.	Medium precipitation with low snow - habitable regions

Finally, visualizing the results, we can observe that clusters b, c, and d are outliers - these are the total 73 extreme regions that PG&E must focus on.

# Conclusion and Recommendations

●	Based on our analysis of weather data, we noticed that there was a significant difference between overall temperatures between 2021 and 2022, indicating that temperatures are increasing due to global warming

●	Weather data follows a cyclical pattern and the trend of weather repeats over time, this helps in the predictability of weather patterns over time

●	There are 73 stations with extreme weather conditions (snow and precipitation) that PG&E must prioritize to ensure seamless power supply during extreme weather events

## File Structure

`AirflowDAG\RealTimeDAG` and `AirflowDAG\HistoricalDataDAG`: This folder includes the Directed Acyclic Graph (DAG) operations used in Apache Airflow for scheduling and automating the ETL pipeline

`BigQueries\Query Analysis`: PDF containing SQL Queries, output screenshots

`HistoricalDataFiles`: CSV's for 3 years of historical data and station level CSV files

`PythonFiles\HistoricalandRealTimeDataExtraction`: Python file containing the data extraction for historical and realtime weather

`PythonFiles\StatsandML`: Python file containing the statistics methods for use cases and machine learning

`Report\Report`: PDF containg the report of the project

`Schema\DB_Schema`: ER diagram screenshot created in MySQL

`Visualizations\Visualizations`: Visualizations screenshots from Looker

## Contact Information

For inquiries or feedback, contact:

- Sakshi Jain: sakshijain070198@gmail.com

Explore the project and leverage insights from the NOAA weather data!
