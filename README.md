# Bellabeat Data Analysis Case Study

## Company Description

Bellabeat was founded in 2013. A high-tech company that manufactures health-focused wellness, smart devices, and products for women. By 2016, the company has grown globally and had become available to many online retailers and also including its own [website](https://bellabeat.com/).

The Bellabeat app provides users with health data related to their activity, sleep, stress, menstrual cycle, and mindfulness habits. This data can help users better understand their current habits and make healthy decisions. The Bellabeat app connects to their line of smart wellness products.

The case study follows the six step data analysis process:

[1. Ask](#1-ask)

[2. Prepare](#2-prepare)

[3. Process](#3-process)

[4. Analyze and Share](#4-analyze-and-share)

[5. Act](#5-act)

## 1. Ask

As a data analyst on the marketing team, I will analyze the data to gain insight into non-Bellabeat's smart devices and focus on one of Bellabeat’s smart devices and apply these insights to a presentation for the marketing strategy. 
These questions will guide our analysis.

- What are some trends in smart device usage?
- How could these trends apply to Bellabeat customers?
- How could these trends help influence Bellabeat marketing strategy?

### 1.1 Stakeholders

- Urška Sršen - Cofounder and Chief Creative Officer
- Sando Mur - Cofounder and on the Executive Team
- Bellabeat marketing analytics team.

## 2. Prepare

The data used is stored in Kaggle under the Fitbit Fitness Tracker Data which is made available through Mobius that contains personal information about their daily activity like heart rate, sleep pattern, and physical activity. The data is organized into 18 CSVs and It includes both wide and long formats. 

Due to some limitations of the data, we might be missing out on a few demographic features like age. We also do not have the height of the users, since [BMI calculation](https://www.calculator.net/bmi-calculator.html?ctype=standard&cage=24&csex=f&cheightfeet=5&cheightinch=1&cpound=110&cheightmeter=180&ckg=65&printit=0&x=88&y=25) considers weight, height, age, and gender. 

We are not sure if the thirty users are handpicked or called at random for the population of data. The data is not current and not direct from Bellabeat.

## 3. Process
### 3.1 Installing the necessary packages and libraries needed for cleaning and analysis.
```bash
#Installing the packages
install.packages('tidyverse') #used for data cleaning
install.packages('lubridate') #for date & time formats
install.packages('janitor') #used for cleaning data
install.packages('formattable')
install.packages('patchwork') #main title for subplots
```

```bash
#Loading the packages
library(tidyverse) #drop_na()
library(lubridate)
library(janitor) #clean_names()
library(dplyr)
library(ggplot2)
library(formattable)
library(patchwork)
```
### 3.2 Loading the datasets
```bash
daily_activity <- read.csv("/cloud/project/dailyActivity_merged.csv")
sleep_day <- read.csv("/cloud/project/sleepDay_merged.csv")
weight_log_info <- read.csv("/cloud/project/weightLogInfo_merged.csv")
hourly_intensity <- read.csv("/cloud/project//hourlyIntensities_merged.csv")
```
### 3.3 Exploring the dataframe
The Summary of daily_activity, sleep_day and weight_log_info.
```bash
head(daily_activity) 
head(sleep_day) 
head(weight_log_info) 
head(hourly_intensity) 
```
### 3.4 Cleaning and formatting the datasets
3.4.1 Converting the snakeCase to camel_case for the three main datasets
```bash
daily_activity <- clean_names(daily_activity)
sleep_day <- clean_names(sleep_day)
weight_log_info <- clean_names(weight_log_info)
hourly_intensity <- clean_names(hourly_intensity)
```
3.4.2 Checking for duplicates
```bash
sum(duplicated(daily_activity))
sum(duplicated(sleep_day))
sum(duplicated(weight_log_info))
sum(duplicated(hourly_intensity))
```
3.4.3 Deleting duplicates
```bash
# Before deleting duplicates
nrow(sleep_day)

#To clear duplicates
sleep_day <- sleep_day %>%
  distinct() %>%
  drop_na()

#After deleting duplicates
nrow(sleep_day)
```
3.4.4 Formatting String Date datatype to Date time datatype
```
daily_activity$activity_date <- as.Date(daily_activity$activity_date,'%m/%d/%y')
sleep_day$sleep_day <- as.Date(sleep_day$sleep_day,'%m/%d/%y')
weight_log_info$date <- as.Date(weight_log_info$date,'%m/%d/%y')
```
3.4.5 Renaming the data column for daily_activity and sleep_day datasets
```
daily_activity <- daily_activity %>%
  rename(date = activity_date)

sleep_day <- sleep_day %>%
  rename(date = sleep_day)
  ```
3.4.6 Converting minutes to hours for total_minutes_asleep in sleep_day dataset
```
sleep_day$total_hours_asleep = round((sleep_day$total_minutes_asleep)/60, digits = 2)
  ```
3.4.7 Dropping total_minutes_asleep in sleep_day dataset
```
sleep_day = subset(sleep_day, select = -c(total_minutes_asleep))
```
3.4.8 Formatting String datatype to Boolean datatype in weight_log_info dataset
  ```
weight_log_info$is_manual_report <- as.logical(weight_log_info$is_manual_report)
  ```  
3.4.9 Formatting activity_hour to date time datatype in hourly intensity dataframe
```
hourly_intensity$activity_hour=as.POSIXct(hourly_intensity$activity_hour, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
```
3.4.10 Separating activity_hour into date and time columns for hourly intensity dataframe
```
hourly_intensity$time <- format(hourly_intensity$activity_hour, format = "%H:%M:%S")
hourly_intensity$date <- format(hourly_intensity$activity_hour, format = "%m/%d/%y")
```
3.4.11 Creating a new column for weekdays in daily_activity dataset
  ```
daily_activity$day_of_week <- wday(daily_activity$date, label = T, abbr = T)
  ```
3.4.12 Merging daily_activity and sleep_day dataset to daily_activity_sleep and daily_activity and weight_log_info to daily_activity_weight
  ```
daily_activity_sleep <- merge(daily_activity, sleep_day, by=c ("id", "date"))
daily_activity_weight <- merge(daily_activity, weight_log_info, by=c ("id", "date"))
  ```
## 4. Analyze and Share
**4.1 Total Steps Vs Sleep**

Let's take a look at how total steps taken will influence how many hours the user will sleep.
```
  ggplot(data = daily_activity_sleep) +
    aes(x=total_steps, y = total_hours_asleep) +
    geom_point(color = '#004c6d') + geom_smooth() + 
    theme(panel.border = element_rect(colour = "black", fill=NA)) +
    labs(x = 'Total Steps', y = 'Total Hours Asleep', 
    title = 'Total Steps vs Total Hours Asleep')
  ```
  
![__results___40_1](https://user-images.githubusercontent.com/116041695/215378158-a870031c-3f90-490a-a88c-0377f081a544.png)

We observe from the graph that the Total steps taken don’t necessarily mean that the person will have a better sleep or the number of hours would be longer than usual.
 
**4.2 Total Steps Vs weekends**

We shall now take a look at which day the users are most and least active.
```
  #options(scipen=) will remove any scientific notations
options(scipen = 999)

ggplot(data=daily_activity_sleep) + 
  geom_col(mapping = aes(x=day_of_week,y=total_steps, fill=day_of_week)) + 
  theme(panel.border = element_rect(colour = "black", fill=NA)) +
  scale_fill_brewer(palette="Set2") +
  theme(legend.position="none") +
  labs(x = 'Day of the week', y = 'Total Steps', 
  title = 'Total no of steps taken in a week')
  ```
![__results___42_1](https://user-images.githubusercontent.com/116041695/215378242-e58269b9-5276-4d17-8a31-f8449b327d26.png)

We can see that the users are most active when the weekend starts and the activity level declines as the week progresses. This could be because of a busy schedule or motivation.

**4.3 Sleep Distribution**

According to [Mayoclinic](https://www.mayoclinic.org/healthy-lifestyle/adult-health/expert-answers/how-many-hours-of-sleep-are-enough/faq-20057898), if one gets:

- Less than 7 hours: Insufficient
- 7 to 8 hours: Well rested
- More than 8 hours: Overslept

```
sleep_log <- sleep_day%>%
  mutate(sleep_log = case_when( 
     total_hours_asleep < 7 ~ "Less than 7 hours",
     total_hours_asleep >= 7 &  total_hours_asleep < 8 ~ "7 to 8 hours", 
     total_hours_asleep >= 8 ~ "More than 8 hours"
  ))
  ```
Now we shall find the percentage of users for each category.
```
sleep_percent <- sleep_log%>%
  group_by(sleep_log) %>%
  summarise(total = n()) %>%
  mutate(totals = sum(total)) %>%
  group_by(sleep_log) %>%
  summarise(sleep_percent = total / totals) %>%
  mutate(percent = scales::percent(sleep_percent))
``` 
Formattable use to turn chr to percent
```
sleep_percent <- sleep_percent %>% mutate(percent=percent(sleep_percent))
```
With sleep_log and percent, let's visualize it in a pie chart.
```
ggplot(data=sleep_percent) + 
  geom_col(mapping = aes(x=sleep_log,y=percent, fill=sleep_log)) + 
  theme(panel.border = element_rect(colour = "black", fill=NA)) +
  scale_fill_brewer(palette="Set2") +
  theme(legend.position="none") +
  labs(x = 'Total Hours Asleep', y = 'Percent', title = 'Sleep Distribution')
```
![__results___48_1](https://user-images.githubusercontent.com/116041695/215378306-f1d00564-e4f4-460b-8aed-db5bebdab9fa.png)

From the pie chart, we can see that only about 27% of the users sleep for a sufficient amount of 7 to 8 hours whereas 44% sleep for less than 7 hours.

**4.4 Total Steps Vs Calories**

Based on the number of steps the user has taken, let's find out how many calories they have burned and if there is a correlation between the two.
```
daily_activity_sleep %>% 
  group_by(total_steps, calories) %>% 
  ggplot(aes(x = total_steps, y = calories, color = calories)) +
  geom_point() +
  geom_smooth() + 
  theme(legend.position = c(.9, .2),
        legend.spacing.y = unit(1, "mm"), 
        panel.border = element_rect(colour = "black", fill=NA),
        legend.background = element_blank(),
        legend.box.background = element_rect(colour = "black")) +
  labs(title = 'Calories Burned by Total Steps Taken',
       y = 'Calories',
       x = 'Total Steps')
```
![__results___51_1](https://user-images.githubusercontent.com/116041695/215378348-3cda8ebe-f18d-4de0-abbe-e7f996b9564d.png)

From the graph, we can see that there is a correlation between Total Steps and Calories. When the user takes more number of steps, more calories are burned.

**4.5 BMI Distribution**

According to standard BMI values from [hdfclife](https://www.hdfclife.com/financial-tools-calculators/bmi-calculator) we have,

- Underweight – BMI less than 18.5
- Normal weight – BMI between 18.5 and 24
- Overweight – BMI between 25 and 29.9
- Obese – BMI above 30
```
weight_log_bmi <- weight_log_info %>%
  mutate(bmi_log = case_when( 
    bmi < 18.5 ~ "Underweight",
    bmi >= 18.5 & bmi < 24 ~ "Normal Weight", 
    bmi >= 24 & bmi < 29.9 ~ "Overweight", 
    bmi >= 30 ~ "Obesity",
  ))
```
Now that we've classified the users by their BMIs. Let's focus on the different users and their BMI ranges.
```
weight_log_users <- weight_log_bmi %>% distinct(id, .keep_all = TRUE)
```
Here we see that there are 8 users who've logged their weight in.
```
weight_bmi_percent <- weight_log_users %>%
  group_by(bmi_log) %>%
  summarise(total = n()) %>%
  mutate(totals = sum(total)) %>%
  group_by(bmi_log) %>%
  summarise(bmi_percent = total / totals) %>%
  mutate(percent = scales::percent(bmi_percent))
```
Formattable use to turn chr to percent
```
weight_bmi_percent <- weight_bmi_percent %>% mutate(percent=percent(bmi_percent))
```
There are 62% overweight people and 25% normal people. To see it better, let's plot a graph.
```
ggplot(weight_bmi_percent,aes(fill=bmi_log,y = percent,x = "")) +
  geom_bar(stat = "identity", width = 1, color = "white") +
  coord_polar("y", start = 0) +
  scale_fill_brewer(palette="Set2") +
  theme_void() +
  geom_text(aes(label = percent, x=1),position = position_stack(vjust = 0.5)) +
  theme(plot.title = element_text(hjust = 0.5, vjust = -5, size = 15, family="TT Times New Roman", face = "bold")) +
  labs(title = "BMI Distribution") +
  guides(fill = guide_legend(title = "BMI Distribution"))

```
![__results___59_1](https://user-images.githubusercontent.com/116041695/215378404-f9d24cc0-be02-4276-a95a-c078178fb823.png)

According to the graph, 62% of people are overweight, 12% are obese, and 25% are normal weight.

**4.6 Weight Vs Total Steps**

Let's take a closer look at weight and total steps to see if there is any correlation between the two.
```
ggplot(data = daily_activity_weight) +
  aes(x=weight_kg, y=total_steps) +
  geom_point(color = '#004c6d') +
  geom_smooth() + 
  theme(panel.border = element_rect(colour = "black", fill=NA)) +
  labs( x = 'Weight in kg', y = 'Total Steps Taken',
       title = 'Total Steps Taken Vs Weight in Kg')

```
![__results___61_1](https://user-images.githubusercontent.com/116041695/215378435-184abfc4-24ee-4c82-b836-12473aeee0d1.png)

From the graph, we can conclude that majority of the users who weigh about 60 to 90 kg are the most active and users who weigh above 80 kg seem to take longer walks.

**4.7 User Type Distribution**

Now we shall take a look into how many users are active according to their average calories burned, average steps taken and average hours asleep.
```
daily_average <- daily_activity_sleep %>%
  group_by(id) %>%
  summarise (mean_total_steps = mean(total_steps),
             mean_total_calories = mean(calories),
             mean_total_sleep = mean(total_hours_asleep))
```
According to 10000 steps,

- Sedentary: Less than 5,000 steps per day
- Low active: Between 5,000 to 7,499 steps per day
- Somewhat active: Between 7,500 to 9,999 steps per day
- Active: More than 10,000 steps per day
- Highly active: More than 12,500

With reference to average total steps taken, let's categorize each user to their respective active level.
```
activity_user_type <- daily_average %>%
  mutate(activity_user_type = case_when( 
    mean_total_steps < 5000 ~ "Sedentary",
    mean_total_steps >= 5000 & mean_total_steps < 7500 ~ "Low active", 
    mean_total_steps >= 7500 & mean_total_steps < 10000 ~ "Somewhat active", 
    mean_total_steps >= 10000 & mean_total_steps < 12500 ~ "Active",
    mean_total_steps >= 12500 ~ "Highly active"
  ))
```
Now that we have put the users in different active levels based on the mean total number of steps taken. Let's find out the percentages for the same.
```
user_type <- activity_user_type %>%
    group_by(activity_user_type) %>%
    summarise(total = n()) %>%
    mutate(totals = sum(total)) %>%
    group_by(activity_user_type) %>%
    summarise(user_percent = total / totals) %>%
    mutate(percent = scales::percent(user_percent))
```
Formattable use to turn chr to percent
```
user_type <- user_type %>% mutate(percent=percent(user_percent))
```
Let's plot a pie chart to see the different active levels and which of each level is highest and which is the lowest.

```
ggplot(user_type,aes(fill=activity_user_type,y = percent,x = "")) +
  geom_bar(stat = "identity", width = 1, color = "white") +
  coord_polar("y", start = 0) +
  scale_fill_brewer(palette="Set2") +
  theme_void() +
  geom_text(aes(label = percent, x=1),position = position_stack(vjust = 0.5)) +
  theme(plot.title = element_text(hjust = 0.5, vjust = -5, size = 15, family="TT Times New Roman", face = "bold")) +
  labs(title = "User Type Distribution") +
  guides(fill = guide_legend(title = "User Type Distribution"))
```
![__results___69_1](https://user-images.githubusercontent.com/116041695/215378483-e1cbeb27-c80c-4880-b0c6-4697a2b65092.png)

From the evenly distributed pie chart, we can tell that the lowest percentage is 4.17% of the users who fall into the highly active category whereas about 37.5% of the users are somewhat active.

**4.8 Active Hours**

Let's plot a graph to see the peak hours

```
hourly_intensity <- hourly_intensity %>%
  group_by(time) %>%
  drop_na() %>%
  summarise(mean_hourly_intensity = mean(total_intensity))
```
```
ggplot(data = hourly_intensity) +
  aes(x=time, y = mean_hourly_intensity) +
  geom_histogram(stat = "identity", fill='#004c6d') +
  theme(panel.border = element_rect(colour = "black", fill=NA)) +  
  theme(axis.text.x = element_text(angle = 90)) +
  labs(x = 'Hour', y = 'Total Intensity', 
       title = 'Total Intensity per hour')
       
ggsave("Active Hours.png")
```
![__results___80_1](https://user-images.githubusercontent.com/116041695/218244003-7bd4fedc-f488-4ead-88ba-bd80440a1468.png)

From the graph, we can see that the peak hours are from 5 pm to 7 pm.

## 5. Act
- The total no of steps taken doesn’t necessarily mean that the person will have a better sleep. Bellabeat app could provide a quiz when the user signs up to help the user understand better how they can improve both their sleep and how much they need to walk per day according to their height, weight, and more.
- Since the users’ motivation to walk during the week drops gradually, the app can provide points or streaks to help motivate the user. The points can be paid towards the membership program.
- From the asleep distribution pattern, we can see that 44% of the users sleep less than 7 hours. Bellabeat can offer articles on their app on how to have a productive day and head to bed on time or articles that can help the user better their sleep pattern.
- The total no of steps taken results in the calories burned. The app can send out alerts that say “they have reached their goal for the day or a word of appreciation”.
- According to the BMI distribution, 5 users are overweight, 2 are of Normal Weight, and 1 is Obese, Bellabeat can implement an alarm to remind the user every day when it is time for a walk or workout. An alert could also be sent out if the user has been sedentary for a while.
