
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
