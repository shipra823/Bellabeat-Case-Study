
# Bellabeat Case Study
#### Author: Shipra Sharma
#### Date: 02/05/2025

<img width="400" alt="bellabeatlogo big" src="https://user-images.githubusercontent.com/125687123/219961609-e96ff4b4-0d98-4248-baa8-f93d70ee7695.png">

This case study follows from the capstone project from the Google Data Analytics Course. I will be using the 6 step data analysis process for this case study: Ask, Prepare, Process, Analyze, Share and Act. 

## Background
Bellabeat is a high tech manufacturer focused on health products for women. As of now, Bellabeat is a small company which would like to to grow in the smart device market. The CEO believes that analyzing smart device data will allow the company to exploit opportunities to grow in a market where technology is always evolving. Therefore, our team has been asked to analyze smart device data to see how consumers are using their smart devices, and to provide high level reconmendations for Bellabeats marketing strategy. 

## 1. Ask 
Business Task: Analyze smart device usage data to see how consumers use non-Bellabeat products. Finalize insights into a report of analysis conducted and presentation on how Bellabeat should proceed with their products and marketing strategies. 

Key stakeholders: women who use health products, Urska (CEO of Bellabeat), Sando (cofounder), executive team, Bellabeat marketing team, team of data analysts. 

## 2. Prepare
Data source: https://www.kaggle.com/datasets/arashnic/fitbit CC0: Public Domain, dataset made available through Mobius.

This dataset was generated by respondents to a distributed survey via Amazon Mechanical Turk between 12/03/2016-12/05/2016. Thirty eligible Fitbit users consented to the submission of personal tracker data. It includes 18 sets of data including types of daily activity. However, only 6 datasets are used: daily activity, weight log, sleep, hourly steps, hourly intensities and hourly calories. Rest of the datasets are irrelevant for this analysis. 

Are there any issues with bias or credibitility of the data source - does it ROCCC? 
- Reliabile: dataset is unreliable. Although central limit theorem applies as the sample size is 30, and can use t test for statistical analysis, a bigger sample size would be more representative of the population. Furthermore, it does not include demographics such as gender, location, age, occupation etc.
- Original: not original data. It is third party dataset from Kaggle and Amazon Mechanical Turk.
- Comprehensive: useful datasets are provided such as physical activity, calories second and hourly activity. These factors are valuable for Fitbit analysis, however, most data is only recorded from Tuesday Wednesday and Thursday which makes it less comprehensive.  
- Current: dataset is not current as it is from 7 years ago (2016). However, may still be useful for analysis. 
- Cited: dataset states it is updated annually however last updated 2 years ago. Data set from Kaggle, user: MÖBIUS. 

Dataset is under CC0: public Domain lisecnce which means creator has copyright law. 

## 3. Process
Tools that will be used: Excel for data cleaning and Rstudio for analysis and creating visualizations. 
To ensure data integrity, limitations of datasets will be accounted for by using excel to clean the data. The cleaned dataset overcomes the limitations and hence contributes to the overall accuracy, completeness and consistency of the data. 

Data cleaning process for each dataset: 
- Daily activity: used the filter tool and found there are 33 IDs and therefore 33 individuals in this dataset, not 30 as the description of the dataset states. 
- Weight log: there are only 8 individuals in this dataset which suggests the other 25 individuals did not input their weight into the Fitbit. This small sample size can bias our results. Used the text to column function to split date and time into separate columns. 
- Sleep: 3 duplicate values were found. These were removed now 410 unique values remain. Only 25 IDs are included and therefore only 25 individuals for sleep studied. Used the text to column function to split date and time into separate columns. Also, SleepDate date was incorrectly formatted, so changed the format into date column. 
- Hourly steps: used the text to column function to split date and time into separate columns.
- Hourly intensity: used the text to column function to split date and time into separate columns.
- Hourly calories: used the text to column function to split date and time into separate columns.
 
Datamapping: in the analysis stage, we need to ensure datasets are compatible so they datasets can be merged for analysis. By using text to column function, we ensure headings and cell format were identical to all other datasets (consistent naming conventions). Also, by formatting all date columns in each dataset to ‘date’ ensures compatibility.  

## 4. Analyze 
Cleaned dataset from Excel has been imported and now ready for analysis. The visualizations will be included in this stage instead of the share stage as visualizations will be created through Rstudio. 

Creating dataframes.
```
daily_activity <- read.csv("dailyActivity_merged.csv")
sleep_day <- read.csv("sleepDay_merged.csv")
hourly_calories <- read.csv("hourlyCalories_merged.csv")
hourly_intensities2 <- read.csv("hourlyIntensities_merged.csv")
hourly_steps <- read.csv("hourlySteps_merged.csv")
weight_log <- read.csv("weightLogInfo_merged.csv")
```
Merging daily activity dataset with sleep dataset.
```
daily_activity_with_sleep <- merge(sleep_day, daily_activity, by="Id")
daily_activity_with_weight <- merge(weight_log, daily_activity, by="Id")
```
Creating summary statistics.
```
daily_activity %>%  
  select(TotalSteps,
         TotalDistance,
         SedentaryMinutes) %>%
  summary()

sleep_day %>%  
  select(TotalSleepRecords,
         TotalMinutesAsleep,
         TotalTimeInBed) %>%
  summary()

hourly_calories %>%  
  select(ActivityHour, Calories) %>%
  summary()

hourly_intensities2 %>%  
  select(ActivityHour, TotalIntensity, AverageIntensity) %>%
  summary()

hourly_steps %>%  
  select(ActivityHour, StepTotal) %>%
  summary()

weight_log %>%  
  select(WeightKg, WeightPounds, Fat, BMI, IsManualReport) %>%
  summary()
```
![summary statistics2](https://user-images.githubusercontent.com/125687123/219881798-4e94d735-2fe6-4056-8f7d-09ece3ad3184.png)

Converting activity date to weekdays.
```
daily_activity <- daily_activity %>% mutate( Weekday = weekdays(as.Date(ActivityDate, "%m/%d/%Y")))
daily_activity$weekday1 <- ordered(daily_activity$Weekday, levels=c("Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"))
activity_data <- daily_activity %>% 
  group_by(Weekday) %>% 
  summarize(count = n())  
```

Plotting weekday activity_data to see which days individuals use tracker the most. 
```
ggplot(activity_data, aes(x=Weekday, y=count)) +
  geom_bar(stat="identity",color="black",fill="purple") +
  labs(title="Daily activity usage", x="Days", y="Count") 
```
![countdays2](https://user-images.githubusercontent.com/125687123/219972431-055dec16-564d-49a0-9da6-460eb958291e.jpeg)

Plotting relationship between steps taken and sedentary minutes.
``` 
ggplot(data=daily_activity, aes(x=TotalSteps, y=SedentaryMinutes)) + geom_point(colour="purple", size=0.5)+geom_smooth(method="lm")+labs(title="Relationship between total steps and sedentary minutes", x="Total steps", y="Sedentary minutes")

```
![sedenminbig](https://user-images.githubusercontent.com/125687123/219973351-3774fffc-2dc5-476c-b1de-7f0de07265d5.jpeg)

Plotting relationship between total steps taken and calories burnt.
```
ggplot(data=daily_activity, aes(x=TotalSteps, y=Calories))+geom_point(color="purple", size=0.5)+ labs(title="Relationship between total steps and calories burnt", x= "Total Steps", y = "Calories")+ geom_smooth(method = 'loess', formula= 'y ~ x')
```
![stepscal](https://user-images.githubusercontent.com/125687123/219950891-b29d50c9-9387-40e5-b387-ecd9a06f5f51.jpeg)

Plotting relationship between total minutes asleep and total time in bed. 
```
ggplot(data=sleep_day, aes(x=TotalMinutesAsleep, y=TotalTimeInBed)) +
  geom_point(colour="purple", size=0.5) +
  labs(title="Relationship between time asleep and time in bed",x="Time Asleep",y="Time in Bed") +
  geom_smooth(method="lm") 
```
![sleepbed2](https://user-images.githubusercontent.com/125687123/219951065-2d9d8a3d-8d5b-4574-b48a-ee8927bffb1a.jpeg)

Finding the absolute number of people getting their recommended number of hours of sleep (7-9hours, 420-540min). 
```
daily_activity_with_sleep %>%
  count(TotalMinutesAsleep < 420)
daily_activity_with_sleep %>%
  count(TotalMinutesAsleep > 540)
```
From absolute values, proportion of individuals are taken and piechart is constructed on excel. 

![less than 420](https://user-images.githubusercontent.com/125687123/219883029-73365026-af2c-4161-bbaf-32c620412134.png)
![greater than 520](https://user-images.githubusercontent.com/125687123/219883054-f5870334-7ec4-460c-8482-25580c5522cf.png)

![piechart6](https://user-images.githubusercontent.com/125687123/219954087-5b599d53-6816-4031-9ca6-6ad01a2acb3a.jpg)

Plotting hourly calories burnt.
```
ggplot(data=hourly_calories, aes(x=time, y= Calories)) + 
  geom_histogram(stat = "identity", fill="purple") +
  theme(axis.text.x = element_text(angle = 90)) +
  labs(title="Average calories burnt by the hour", x="Time", y="Calories")
```
![calhours](https://user-images.githubusercontent.com/125687123/219955148-16832cc6-c1cc-4917-b85d-4b39b80fea20.jpeg)

## 5. Share
Findings from analysis stage will be shared and this is supported with the visualizations created above. 
### Key Findings
- Summary statistics show majority of individuals are lightly active and average daily step is 7638. This is less than the recommended number of steps individuals should take per day. 
- Daily activity usage shows users record more data on Tuesday, Wednesday and Thursday. This shows an unequal distribution in the week and may be due to users not using their devices on weekends due to leisure time. Howvever, Monday is still lower so this could suggest a limitation to the dataset of missing data. 
- Relationship between sedentary minutes and total steps is negative, as expected. 
- Relationship between total daily steps and total daily calories is positive. 
- Relationship between total time asleep and total time in bed is positive, suggesting individuals do not struggle much to fall asleep. Average time asleep is 6.98hours and average time in bed is 7.64hours. Piechart shows that a large amount of individuals, 54%, are not getting the recommended amount of sleep whilst 46% are. 
- Most calories are burnt in the evening from 17:00 to 19:00. This could be due to individuals going to the gym/exercising after work as they are likely to be sedentary during work hours. 

## 6. Act 
Findings will be acted on by creating a presentation to stakeholders summarizing the analysis. From these findings, high-level recommendations will be made on how Bellabeat should go forward in improving their marketing strategy. 

### Recommendations
- Encourage individuals to increase daily steps to burn more calories. Implement a 'daily step goal' and notify users when they have completed it with encouraging messages. 
- Notify individuals on days where they did not get their recommended number of hours of sleep and encourage them to do so. 
- A feauture which includes ways on how individuals can be more active by suggesting HIIT workouts and different types of exercise. A notification can be enabled if the individual has been sedentary for too long. 
- Implement an 'add friends' feature where they can compare activity levels, steps and calorie burn for friendly competition.   
- As individuals are mostly active during 17:00-19:00, Bellabeat can create an 'evening workout routines for the gym/home.'


Importantly, for the above recommendations to be made, Bellabeat should invest a large sum into their technology of data science in developing their algorithms.  As technologic advancements are accelearting in the economy today, by investing in their technology to improve their services, Bellabeat will stay ahead of competitors. Therefore, they should also promote their product by use of social media. 
