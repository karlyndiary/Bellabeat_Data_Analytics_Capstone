
# Bellabeat Data Analysis Case Study

## 1. Company Description

Bellabeat was founded in 2013. A high-tech company that manufactures health-focused wellness, smart devices, and products for women. By 2016, the company has grown globally and had become available to many online retailers and also including its own [website](https://bellabeat.com/).

The Bellabeat app provides users with health data related to their activity, sleep, stress, menstrual cycle, and mindfulness habits. This data can help users better understand their current habits and make healthy decisions. The Bellabeat app connects to their line of smart wellness products.

## 2. Ask
As a data analyst on the marketing team, I will analyze the data to gain insight into non-Bellabeat's smart devices and focus on one of Bellabeat’s smart devices and apply these insights to a presentation for the marketing strategy. 
These questions will guide our analysis.

- What are some trends in smart device usage?
- How could these trends apply to Bellabeat customers?
- How could these trends help influence Bellabeat marketing strategy?

### 2.1 Stakeholders

- Urška Sršen - Cofounder and Chief Creative Officer
- Sando Mur - Cofounder and on the Executive Team
- Bellabeat marketing analytics team.

## 3. Prepare

The data used is stored in Kaggle under the Fitbit Fitness Tracker Data which is made available through Mobius that contains personal information about their daily activity like heart rate, sleep pattern, and physical activity. The data is organized into 18 CSVs and It includes both wide and long formats. 

Due to some limitations of the data, we might be missing out on a few demographic features like age. We also do not have the height of the users, since [BMI calculation](https://www.calculator.net/bmi-calculator.html?ctype=standard&cage=24&csex=f&cheightfeet=5&cheightinch=1&cpound=110&cheightmeter=180&ckg=65&printit=0&x=88&y=25) considers weight, height, age, and gender. 

We are not sure if the thirty users are handpicked or called at random for the population of data. The data is not current and not direct from Bellabeat.

## 4. Process
### 4.1 Installing the necessary packages and libraries needed for cleaning and analysis.
```bash
#Installing the packages
install.packages('tidyverse') #used for data cleaning
install.packages('lubridate') #for date & time formats
install.packages('janitor') #used for cleaning data
```

```bash
#Loading the packages
library(tidyverse) #drop_na()
library(lubridate)
library(janitor) #clean_names()
library(dplyr)
library(ggplot2)
```
### 4.2 Loading the datasets
```bash
daily_activity <- read.csv("/cloud/project/dailyActivity_merged.csv")
sleep_day <- read.csv("/cloud/project/sleepDay_merged.csv")
weight_log_info <- read.csv("/cloud/project/weightLogInfo_merged.csv")
```
### 4.3 Exploring the dataframe
The Summary of daily_activity, sleep_day and weight_log_info.
```bash
str(daily_activity) 
str(sleep_day) 
str(weight_log_info) 
```
### 4.4 Cleaning and formatting the datasets
Converting the snakeCase to camel_case for the three main datasets
```bash
daily_activity <- clean_names(daily_activity)
sleep_day <- clean_names(sleep_day)
weight_log_info <- clean_names(weight_log_info)
```
Checking and removing duplicates
```bash
#To check for duplicates
sum(duplicated(sleep_day))

#To clear duplicates
sleep_day <- sleep_day %>%
  distinct() %>%
  drop_na()

#After deleting duplicates
nrow(sleep_day)
```
Formatting String Date datatype to Date time datatype for daily_activity, sleep_day and weight_log_info
```bash
daily_activity$activity_date <- as.Date(daily_activity$activity_date,'%m/%d/%y')
sleep_day$sleep_day <- as.Date(sleep_day$sleep_day,'%m/%d/%y')
weight_log_info$date <- as.Date(weight_log_info$date,'%m/%d/%y')
```
Renaming the data column for daily_activity and sleep_day
```bash
daily_activity <- daily_activity %>%
  rename(date = activity_date)

sleep_day <- sleep_day %>%
  rename(date = sleep_day)
  ```
Converting minutes to hours for total_minutes_asleep
```bash
sleep_day$total_hours_asleep = round((sleep_day$total_minutes_asleep)/60, digits = 2)
  ```
