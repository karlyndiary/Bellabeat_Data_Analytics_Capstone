
# Bellabeat Data Analysis Case Study

## Company Description

Bellabeat was founded in 2013. A high-tech company that manufactures health-focused wellness, smart devices, and products for women. By 2016, the company has grown globally and had become available to many online retailers and also including its own [website](https://bellabeat.com/).

The Bellabeat app provides users with health data related to their activity, sleep, stress, menstrual cycle, and mindfulness habits. This data can help users better understand their current habits and make healthy decisions. The Bellabeat app connects to their line of smart wellness products.

The case study follows the six step data analysis process:

* [1. Ask](#1-ask)
* [2. Prepare](#2-prepare)
* [3. Process](#3-process)
* [4. Analyze and Share](#4-analyze-and-share)
* [5. Act](#5-act)

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
```

```bash
#Loading the packages
library(tidyverse) #drop_na()
library(lubridate)
library(janitor) #clean_names()
library(dplyr)
library(ggplot2)
library(formattable)
```
### 3.2 Loading the datasets
```bash
daily_activity <- read.csv("/cloud/project/dailyActivity_merged.csv")
sleep_day <- read.csv("/cloud/project/sleepDay_merged.csv")
weight_log_info <- read.csv("/cloud/project/weightLogInfo_merged.csv")
```
### 3.3 Exploring the dataframe
The Summary of daily_activity, sleep_day and weight_log_info.
```bash
head(daily_activity) 
head(sleep_day) 
head(weight_log_info) 
```
### 3.4 Cleaning and formatting the datasets
3.4.1 Converting the snakeCase to camel_case for the three main datasets
```bash
daily_activity <- clean_names(daily_activity)
sleep_day <- clean_names(sleep_day)
weight_log_info <- clean_names(weight_log_info)
```
3.4.2 Checking for duplicates
```bash
sum(duplicated(daily_activity))
sum(duplicated(sleep_day))
sum(duplicated(weight_log_info))
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
```bash
daily_activity$activity_date <- as.Date(daily_activity$activity_date,'%m/%d/%y')
sleep_day$sleep_day <- as.Date(sleep_day$sleep_day,'%m/%d/%y')
weight_log_info$date <- as.Date(weight_log_info$date,'%m/%d/%y')
```
3.4.5 Renaming the data column for daily_activity and sleep_day datasets
```bash
daily_activity <- daily_activity %>%
  rename(date = activity_date)

sleep_day <- sleep_day %>%
  rename(date = sleep_day)
  ```
3.4.6 Converting minutes to hours for total_minutes_asleep in sleep_day dataset
```bash
sleep_day$total_hours_asleep = round((sleep_day$total_minutes_asleep)/60, digits = 2)
  ```
3.4.7 Dropping total_minutes_asleep in sleep_day dataset
```
sleep_day = subset(sleep_day, select = -c(total_minutes_asleep))
```
3.4.8 Formatting String datatype to Boolean datatype in weight_log_info dataset
  ```bash
weight_log_info$is_manual_report <- as.logical(weight_log_info$is_manual_report)
  ```
3.4.9 Creating a new column for weekdays in daily_activity dataset
  ```bash
daily_activity$day_of_week <- wday(daily_activity$date, label = T, abbr = T)
  ```
3.4.10 Merging daily_activity and sleep_day dataset to daily_activity_sleep and daily_activity and weight_log_info to daily_activity_weight
  ```bash
daily_activity_sleep <- merge(daily_activity, sleep_day, by=c ("id", "date"))
daily_activity_weight <- merge(daily_activity, weight_log_info, by=c ("id", "date"))
  ```
## 4. Analyze and Share
**4.1 Total Steps Vs Sleep**

Let's take a look at how total steps taken will influence how many hours the user will sleep.
```bash
  ggplot(data = daily_activity_sleep) +
    aes(x=total_steps, y = total_hours_asleep) +
    geom_point(color = '#004c6d') + geom_smooth() + 
    theme(panel.border = element_rect(colour = "black", fill=NA)) +
    labs(x = 'Total Steps', y = 'Total Hours Asleep', 
    title = 'Total Steps vs Total Hours Asleep')
  ```
  ![image](https://www.kaggleusercontent.com/kf/115340771/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..7bUx0B3-P2anyMF-cJdrmA.Ha9AJv4uHj-xFqDi9RGy3Rac0dCdnVaapYTPve4FxsJ3bVnMQFlCm5nxDJ4LIqChlv-Nnb6pdU8cAxkZkPYaL8UY4qBD-DXCAH5Q1dg1qyjNr_M4ZhDqpZ72W-0lflQaKeDjlVJ1UVWcNNoMYCwTWMrjh60Ec8tyoqG_n16VRleEyYMf5hLftHCCmp-HTeHL3mbtwSF27mVs4K_6acxS2zTiLVAefzLm6SAZM5IjIKicwsrGQXyBxra2itlyC5m8dEAfRCFpaY-1K-9e649iYttTZ8J3x0HHlGx5ikZCmwG-wb4NdVWdxmG6iKBL7QjYI4O0MUQQVN9qK1LLMxwugP6Km_AunYILtaS98rbb3N5x8MEnBwHOdx3uBxRV52osrfH70sEqnNzBkO7fbPPo_8kFy5dLqWnsbP6Cc6O9oHoZP9bVNTQMJxykuMTE-m2j0ZSVrH19hiWJKzPP55tG5HHUGxgjucvQXE1jteA5_4l8mmVzLHJPEFIMOycXojZlGI0vXY6CxUm9EkNv9LBnFphB7myL9PGv88kRRoqclU5e7N-WqhUNFUWY3fE2AxYoEkzRyOeA8gC8tqoVxyvM-fhQtGAmpmNlzCQn5uJfSBK9ir5erIILJCnfCDpKWAGRP05o-BntXSQUT__UFHY39hdo4WUQv0pUyQgmSpEcNhwfSY4WMScTV6DEGT3T205L.rnb5xXM2dJdWxrzJZWCBpQ/__results___files/__results___38_1.png)
  
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
 ![image](https://www.kaggleusercontent.com/kf/115340771/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..cuQr6xdllkrQpq-NAr9ZrA.8QouZNgsYzo3g-c-7tOtS4l661qsRjVSF3XyFLZ4bepkDiRQ1q1cE_gLkzNH4eND_cnxkE1LwI-d2jvJ44OIx0zJ1wnW4e8EcJzd3xPJcBgeUt7m7Sp9p2rt-J4cbaCPB0hrnrop6MAReHhLCs7rSCUdZR5mVG6ysYOzy5yWHjGgSNbBEogJ5MBnfnKM74xiyO0pZu76nSLgSxwmN2DPiu8mqWucGSJrKWJrDxBb7BTRmrx-kw66FvF1m8jyftEj8EEmDGPhPtoDMdvpxs73H73N9KRnY3WVDGB1vfonkdi_l2z12gp6Q9gpHEDG8Nux96MlKQWQLWYkkWleQtugRdvGFN5e2KRAXEJoeC3wTO5dgKVcvGEkd0Wu157VR8-ung2OggQ67IzUmrtjdFbgsIe7WdnPuBvr-B-BP5w3AYXqhBUqPoxQvDXrOVFCteqInkr5bSHy4IOc-k3VPqFjYJ-1aFfIJnrFWRUP4L0y3DXUXyDp_tFNuYiIvNRQ47NoBnN_BpRA1F2OKJmY3bLWxWnxhzwJJ8h4DKrWuTsSa46NcQvamBj1bj12d9T_kbxvbyUeYw55wDxg-GLJNYfOjJEWGgz9q-rRd-1JW4Px84ojpiU1qo1z70xfiCqVcG9jesYSqbvRvv2gBi6uKl8wDa0Pdh8X57NmKWD_N3RY2BXrG0TqUq2pDb3Xv5jlBd2k.JYXUuuHA-Z_wUWbiE_2oOQ/__results___files/__results___40_1.png)

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
#formattable use to turn chr to percent
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
![image](https://www.kaggleusercontent.com/kf/116335165/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..If5VLmG4vgKShUdtmwQ8qw.66RXmZf_wWIXvIhVzEuvcdSxsjWysWuj_yNE1YevJEv2Hs4q1fqdc4GEuPuAMMP2Hji_yP8HCruO4_h7fR8BEYxkGOX-vJShwaSSlE_ozG150Vkczqw1VPFqKq83n0GpIEdTX8ffrp1ZVPFi2Q8NC1GHVIkVtTnDsiSNmP4yWhLGWn47X8A3sXfYe4TMQtkvOn5DHXQUooHlx5N3XhzUwi3-VOAVHTDDF9AM7sd9rbgtPjdoi-2e4ANMxRIfZKlKOSRn7zY3nPbEI4bxNvrph21mDxkW_N1NEuiHgQiRc6alrT4T_cfLjAezQAqSC8Q6h2V_FnbpJCumWDt7o97L2QBHboyytsLt5eH6ActeNusBMi73oY3lCd05av6pq44Xtr1_-0VUUJ5yfgkZoBYXYktIt_DPv3LFeb5-W4HYjsgrqO80GJ6X13ZpUOtlARKkh8awEwUfJPmvDZtOQAJLYj0-c491VFORkGSEmCZhp1Z1EMWr6av1dlMnvJDTr6m3alF2jXsZAG2p3GLELSQEjB3lILfFY3EqFoQ3I20-rYYb8IqBLN6nOyTNhn0U9yMzmvRqMDtBTKXv_FquWrT8ouPa2WJkcr-CxLENLzjGW_kaBYLPq3faUf_MN_xgiZ-I55CdnvXuA4v2FTwzEroQJak_Pkf1f_Xy2ppc-uXVitivLARDW_wqDxubcwtcYhJ_.j1yTDo7M_1XBIw5Ig_w-VA/__results___files/__results___48_1.png)

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
![image](https://www.kaggleusercontent.com/kf/116335165/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..If5VLmG4vgKShUdtmwQ8qw.66RXmZf_wWIXvIhVzEuvcdSxsjWysWuj_yNE1YevJEv2Hs4q1fqdc4GEuPuAMMP2Hji_yP8HCruO4_h7fR8BEYxkGOX-vJShwaSSlE_ozG150Vkczqw1VPFqKq83n0GpIEdTX8ffrp1ZVPFi2Q8NC1GHVIkVtTnDsiSNmP4yWhLGWn47X8A3sXfYe4TMQtkvOn5DHXQUooHlx5N3XhzUwi3-VOAVHTDDF9AM7sd9rbgtPjdoi-2e4ANMxRIfZKlKOSRn7zY3nPbEI4bxNvrph21mDxkW_N1NEuiHgQiRc6alrT4T_cfLjAezQAqSC8Q6h2V_FnbpJCumWDt7o97L2QBHboyytsLt5eH6ActeNusBMi73oY3lCd05av6pq44Xtr1_-0VUUJ5yfgkZoBYXYktIt_DPv3LFeb5-W4HYjsgrqO80GJ6X13ZpUOtlARKkh8awEwUfJPmvDZtOQAJLYj0-c491VFORkGSEmCZhp1Z1EMWr6av1dlMnvJDTr6m3alF2jXsZAG2p3GLELSQEjB3lILfFY3EqFoQ3I20-rYYb8IqBLN6nOyTNhn0U9yMzmvRqMDtBTKXv_FquWrT8ouPa2WJkcr-CxLENLzjGW_kaBYLPq3faUf_MN_xgiZ-I55CdnvXuA4v2FTwzEroQJak_Pkf1f_Xy2ppc-uXVitivLARDW_wqDxubcwtcYhJ_.j1yTDo7M_1XBIw5Ig_w-VA/__results___files/__results___51_1.png)

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
#formattable use to turn chr to percent
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
![image](https://www.kaggleusercontent.com/kf/116335165/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..If5VLmG4vgKShUdtmwQ8qw.66RXmZf_wWIXvIhVzEuvcdSxsjWysWuj_yNE1YevJEv2Hs4q1fqdc4GEuPuAMMP2Hji_yP8HCruO4_h7fR8BEYxkGOX-vJShwaSSlE_ozG150Vkczqw1VPFqKq83n0GpIEdTX8ffrp1ZVPFi2Q8NC1GHVIkVtTnDsiSNmP4yWhLGWn47X8A3sXfYe4TMQtkvOn5DHXQUooHlx5N3XhzUwi3-VOAVHTDDF9AM7sd9rbgtPjdoi-2e4ANMxRIfZKlKOSRn7zY3nPbEI4bxNvrph21mDxkW_N1NEuiHgQiRc6alrT4T_cfLjAezQAqSC8Q6h2V_FnbpJCumWDt7o97L2QBHboyytsLt5eH6ActeNusBMi73oY3lCd05av6pq44Xtr1_-0VUUJ5yfgkZoBYXYktIt_DPv3LFeb5-W4HYjsgrqO80GJ6X13ZpUOtlARKkh8awEwUfJPmvDZtOQAJLYj0-c491VFORkGSEmCZhp1Z1EMWr6av1dlMnvJDTr6m3alF2jXsZAG2p3GLELSQEjB3lILfFY3EqFoQ3I20-rYYb8IqBLN6nOyTNhn0U9yMzmvRqMDtBTKXv_FquWrT8ouPa2WJkcr-CxLENLzjGW_kaBYLPq3faUf_MN_xgiZ-I55CdnvXuA4v2FTwzEroQJak_Pkf1f_Xy2ppc-uXVitivLARDW_wqDxubcwtcYhJ_.j1yTDo7M_1XBIw5Ig_w-VA/__results___files/__results___59_1.png)

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
![image](https://www.kaggleusercontent.com/kf/116335165/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..If5VLmG4vgKShUdtmwQ8qw.66RXmZf_wWIXvIhVzEuvcdSxsjWysWuj_yNE1YevJEv2Hs4q1fqdc4GEuPuAMMP2Hji_yP8HCruO4_h7fR8BEYxkGOX-vJShwaSSlE_ozG150Vkczqw1VPFqKq83n0GpIEdTX8ffrp1ZVPFi2Q8NC1GHVIkVtTnDsiSNmP4yWhLGWn47X8A3sXfYe4TMQtkvOn5DHXQUooHlx5N3XhzUwi3-VOAVHTDDF9AM7sd9rbgtPjdoi-2e4ANMxRIfZKlKOSRn7zY3nPbEI4bxNvrph21mDxkW_N1NEuiHgQiRc6alrT4T_cfLjAezQAqSC8Q6h2V_FnbpJCumWDt7o97L2QBHboyytsLt5eH6ActeNusBMi73oY3lCd05av6pq44Xtr1_-0VUUJ5yfgkZoBYXYktIt_DPv3LFeb5-W4HYjsgrqO80GJ6X13ZpUOtlARKkh8awEwUfJPmvDZtOQAJLYj0-c491VFORkGSEmCZhp1Z1EMWr6av1dlMnvJDTr6m3alF2jXsZAG2p3GLELSQEjB3lILfFY3EqFoQ3I20-rYYb8IqBLN6nOyTNhn0U9yMzmvRqMDtBTKXv_FquWrT8ouPa2WJkcr-CxLENLzjGW_kaBYLPq3faUf_MN_xgiZ-I55CdnvXuA4v2FTwzEroQJak_Pkf1f_Xy2ppc-uXVitivLARDW_wqDxubcwtcYhJ_.j1yTDo7M_1XBIw5Ig_w-VA/__results___files/__results___61_1.png)

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
#formattable use to turn chr to percent
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

![image](https://www.kaggleusercontent.com/kf/116335165/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..If5VLmG4vgKShUdtmwQ8qw.66RXmZf_wWIXvIhVzEuvcdSxsjWysWuj_yNE1YevJEv2Hs4q1fqdc4GEuPuAMMP2Hji_yP8HCruO4_h7fR8BEYxkGOX-vJShwaSSlE_ozG150Vkczqw1VPFqKq83n0GpIEdTX8ffrp1ZVPFi2Q8NC1GHVIkVtTnDsiSNmP4yWhLGWn47X8A3sXfYe4TMQtkvOn5DHXQUooHlx5N3XhzUwi3-VOAVHTDDF9AM7sd9rbgtPjdoi-2e4ANMxRIfZKlKOSRn7zY3nPbEI4bxNvrph21mDxkW_N1NEuiHgQiRc6alrT4T_cfLjAezQAqSC8Q6h2V_FnbpJCumWDt7o97L2QBHboyytsLt5eH6ActeNusBMi73oY3lCd05av6pq44Xtr1_-0VUUJ5yfgkZoBYXYktIt_DPv3LFeb5-W4HYjsgrqO80GJ6X13ZpUOtlARKkh8awEwUfJPmvDZtOQAJLYj0-c491VFORkGSEmCZhp1Z1EMWr6av1dlMnvJDTr6m3alF2jXsZAG2p3GLELSQEjB3lILfFY3EqFoQ3I20-rYYb8IqBLN6nOyTNhn0U9yMzmvRqMDtBTKXv_FquWrT8ouPa2WJkcr-CxLENLzjGW_kaBYLPq3faUf_MN_xgiZ-I55CdnvXuA4v2FTwzEroQJak_Pkf1f_Xy2ppc-uXVitivLARDW_wqDxubcwtcYhJ_.j1yTDo7M_1XBIw5Ig_w-VA/__results___files/__results___69_1.png)

From the evenly distributed pie chart, we can tell that the lowest percentage is 4.17% of the users who fall into the highly active category whereas about 37.5% of the users are somewhat active.

## 5. Act
- The total no of steps taken doesn’t necessarily mean that the person will have a better sleep. Bellabeat app could provide a quiz when the user signs up to help the user understand better how they can improve both their sleep and how much they need to walk per day according to their height, weight, and more.
- Since the users’ motivation to walk during the week drops gradually, the app can provide points or streaks to help motivate the user. The points can be paid towards the membership program.
- From the asleep distribution pattern, we can see that 44% of the users sleep less than 7 hours. Bellabeat can offer articles on their app on how to have a productive day and head to bed on time or articles that can help the user better their sleep pattern.
- The total no of steps taken results in the calories burned. The app can send out alerts that say “they have reached their goal for the day or a word of appreciation”.
- According to the BMI distribution, 5 users are overweight, 2 are of Normal Weight, and 1 is Obese, Bellabeat can implement an alarm to remind the user every day when it is time for a walk or workout. An alert could also be sent out if the user has been sedentary for a while.
