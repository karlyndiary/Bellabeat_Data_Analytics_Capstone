# Bellabeat Data Analytics Capstone

## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:


# 1. Document description 

This document outlines all of the steps taken to clean and transform Cyclistic's raw datasets to prepare the data for the next stage of analysis. For the purpose of this case study, only data collected between August 2020 - July 2021 will be assessed. The dataset description can be located [here.](https://www.divvybikes.com/system-data)

Please note that Cyclistic is a fictional company. Raw data has been collected by Motivate International Inc, the company which operates the City of Chicago's Divvy bicycle sharing service. The license to use this public dataset can be found [here.](https://www.divvybikes.com/data-license-agreement)

## 2. Loading necessary libraries and datasets

### 2.1 Loading libraries
```{r load packages}
library(tidyverse) #drop_na()
library(lubridate)
library(janitor) #clean_names()
library(dplyr)
library(ggplot2)
library(highcharter)
```

### 2.1 Loading Datasets

```{r load datasets}
daily_activity <- read.csv("/cloud/project/dailyActivity_merged.csv")
sleep_day <- read.csv("/cloud/project/sleepDay_merged.csv")
weight_log_info <- read.csv("/cloud/project/weightLogInfo_merged.csv")
```

## 3. Exploring and Cleaning the data

### 3.1 Summary of dataframes
```{r explore dataframes}
str(daily_activity) 
str(sleep_day)
str(weight_log_info) 
```

### 3.2 Changing case from camel case to snake case
```{r changing case}
daily_activity <- clean_names(daily_activity)
sleep_day <- clean_names(sleep_day)
weight_log_info <- clean_names(weight_log_info)
```

### 3.3 Checking for duplicates
```{r}
sum(duplicated(daily_activity))
sum(duplicated(sleep_day))
sum(duplicated(weight_log_info))
```

### 3.4 Deleting duplicates
```{r}
# Before deleting duplicates
nrow(sleep_day)

# Command to clear duplicates
sleep_day <- sleep_day %>%
  distinct() %>%
  drop_na()

# After deleting duplicates
nrow(sleep_day)
```

### 3.5 Changing datatype for date from char to date datatype
```{r Changing datatype for date from char to date datatype}
daily_activity$activity_date <- as.Date(daily_activity$activity_date,'%m/%d/%y')
sleep_day$sleep_day <- as.Date(sleep_day$sleep_day,'%m/%d/%y')
weight_log_info$date <- as.Date(weight_log_info$date,'%m/%d/%y')
```

### 3.6 Renaming column activity_date and sleep_day to date
```{r renaming column activity_date and sleep_day to date}
daily_activity <- daily_activity %>%
  rename(date = activity_date)

sleep_day <- sleep_day %>%
  rename(date = sleep_day)
```

### 3.7 Converting minutes to hours for total_minutes_asleep
```{r}
#total hours asleep
sleep_day$total_hours_asleep = round((sleep_day$total_minutes_asleep)/60, digits = 2)

str(sleep_day)
```

### 3.8 Formatting String datatype to Boolean datatype
```{r}
weight_log_info$is_manual_report <- as.logical(weight_log_info$is_manual_report)

str(weight_log_info)
```
### 3.9 Creating a new column for weekdays
```{r}
# Adding a new column for weekdays
daily_activity$day_of_week <- wday(daily_activity$date, label = T, abbr = T)

# After adding the weekday column
str(daily_activity)
```

### 3.10 Merging daily_activity and sleep_day dataset
```{r}
daily_activity_sleep <- merge(daily_activity, sleep_day, by=c ("id", "date"))
daily_activity_weight <- merge(daily_activity, weight_log_info, by=c ("id", "date"))

str(daily_activity_sleep)
str(daily_activity_weight)
```

## 4 Analysis

### 4.1 Total Steps Vs Sleep
```{r amount of sleep gained by the total steps taken}
ggplot(data = daily_activity_sleep) +
  aes(x=total_steps, y = total_minutes_asleep) +
  geom_point(color = '#004c6d') +
  geom_smooth() + 
  theme(panel.border = element_rect(colour = "black", fill=NA)) +
  labs(x = 'Total Steps', y = 'Total Minutes Asleep', 
       title = 'Total Steps vs Total Minutes Asleep')

ggsave('Total Steps vs Total Minutes Asleep.png')
```

## 4.2 Total Steps Vs Weekday
```{r total steps taken in a week}
#options(scipen=) will remove any scientific notations
options(scipen = 999)

ggplot(data=daily_activity_sleep) + 
  geom_col(mapping = aes(x=day_of_week,y=total_steps, fill=day_of_week)) + 
  theme(panel.border = element_rect(colour = "black", fill=NA)) +
  scale_fill_brewer(palette="Set2") +
  theme(legend.position="none") +
  labs(x = 'Day of the week', y = 'Total Steps', 
  title = 'Total no of steps taken in a week')

ggsave("Total no of steps taken in a week.png")
```

## 4.3 Sleep Distribution
```{r finding how many hours of sleep each user gets}
sleep_log <- sleep_day%>%
  mutate(sleep_log = case_when( 
     total_hours_asleep < 7 ~ "Less than 7 hours",
     total_hours_asleep >= 7 &  total_hours_asleep < 8 ~ "7 to 8 hours", 
     total_hours_asleep >= 8 ~ "More than 8 hours"
  ))

str(sleep_log)
```

```{r finding the percentage of sleep}
# percentage 
sleep_percent <- sleep_log%>%
  group_by(sleep_log) %>%
  summarise(total = n()) %>%
  mutate(totals = sum(total)) %>%
  group_by(sleep_log) %>%
  summarise(sleep_percent = total / totals) %>%
  mutate(percent = scales::percent(sleep_percent))

head(sleep_percent)
```

```{r sleep distribution}
ggplot(data=sleep_percent) + 
  geom_col(mapping = aes(x=sleep_log,y=percent, fill=sleep_log)) + 
  theme(panel.border = element_rect(colour = "black", fill=NA)) +
  scale_fill_brewer(palette="Set2") +
  theme(legend.position="none") +
  labs(x = 'Total Hours Asleep', y = 'Percent', title = 'Sleep Distribution')

ggsave("Sleep Distribution.png")
```

## 4.4 Total Steps Vs Calories
```{r total stesp taken to burn calories}
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

ggsave("Calories Burned by Total Steps Taken.png")
```
## 4.5 BMI Distribution
```{r determine if the user is healthy, underweight or obese}
weight_log_bmi <- weight_log_info %>%
  mutate(bmi_log = case_when( 
    bmi < 18.5 ~ "Underweight",
    bmi >= 18.5 & bmi < 24 ~ "Normal Weight", 
    bmi >= 24 & bmi < 29.9 ~ "Overweight", 
    bmi >= 30 ~ "Obesity",
  ))

str(weight_log_bmi)
```
### Distinct users
```{r list of all unique users}
weight_log_users <- weight_log_bmi %>% distinct(id, .keep_all = TRUE)
weight_log_bmi %>% group_by(id) %>% filter(row_number() == 1)
```

### Percentage 
```{r weight percentage of each user}
weight_bmi_percent <- weight_log_users %>%
  group_by(bmi_log) %>%
  summarise(total = n()) %>%
  mutate(totals = sum(total)) %>%
  group_by(bmi_log) %>%
  summarise(bmi_percent = total / totals) %>%
  mutate(percent = scales::percent(bmi_percent))

head(weight_bmi_percent)
```

### BMI Distribution
```{r bmi distribution}
ggplot(weight_bmi_percent,aes(fill=bmi_log,y = percent,x = "")) +
  geom_bar(stat = "identity", width = 1, color = "white") +
  coord_polar("y", start = 0) +
  scale_fill_brewer(palette="Set2") +
  theme_void() +
  geom_text(aes(label = percent, x=1),position = position_stack(vjust = 0.5)) +
  theme(axis.title.x= element_blank(),
        axis.title.y = element_blank(),
        panel.border = element_blank(), 
        panel.grid = element_blank(), 
        axis.ticks = element_blank(),
        axis.text.x = element_blank(),
        plot.title = element_text(hjust = 0.5, vjust = -5, size = 15, family="TT Times New Roman", face = "bold")) +
  labs(title = "BMI Distribution") +
  guides(fill = guide_legend(title = "BMI Distribution"))

ggsave("BMI Distribution.png")
```
## 4.6 Weight Vs Total Steps
```{r}
ggplot(data = daily_activity_weight) +
  aes(x=weight_kg, y=total_steps) +
  geom_point(color = '#004c6d') +
  geom_smooth() + 
  theme(panel.border = element_rect(colour = "black", fill=NA)) +
  labs( x = 'Weight in kg', y = 'Total Steps Taken',
       title = 'Total Steps Taken Vs Weight in Kg')

ggsave("weight vs total steps.png")
```









