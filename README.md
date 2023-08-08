# Bellabeat-Case-Study
Case Study from Google Data Analytics professional certificate with Coursera

# Introduction
This project has been performed for Bellabeat, a high-tech company that manufactures health-focused smart products.
Using technology that informs and inspires women around the world. Collecting data on activity, sleep, stress, and reproductive health has allowed Bellabeat to empower
women with knowledge about their own health and habits. Since it was founded in 2013, Bellabeat has grown rapidly and
quickly positioned itself as a tech-driven wellness company for women.


They would like to analyze Bellabeat’s available consumer data to reveal more opportunities for growth. As they want to focus on how people are already using their smart devices to identify trends that can help Bellabeat's marketing strategy.

## Questions for the Analysis:
1. What are some trends in smart device usage?
2. How could these trends apply to Bellabeat customers?
3. How could these trends help influence Bellabeat's marketing strategy?

The Business task this time is to identify opportunities for improvement and growth based on trends in smart device usage and user habits.

# Preparing Data

For this analysis, I've been provided with a Fitbit personal fitness tracker dataset, available on [Kaggle](https://www.kaggle.com/datasets/arashnic/fitbit.).
Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes information
about daily activity, steps, and heart rate.

The dataset contains a small sample since only 30 users' data was collected; additionally, the data was collected in 2016, so it is not updated.

# Processing data

The Data files considered were `DailyActivity_merged.csv` and `SleepDay_merged.csv`. Initially `weightLogInfo_merged.csv` was also considered but was discarted later since it contains data for only 8 users.

### Software used
SQL was the tool used to clean, transform and merge the data, through BigQuery, from Google. 
When trying to import the datasets a problem was encuntered: File `Sleepday_merged.csv` Date format 'DD/MM/YYYY hh:mm:ss _tt_' was not readable by SQL . So I converted the date to DD/MM/YYYY only.
While converting files, no missing values were encountered. 

### Cleaning

Dataset `DailyActivity_merged.csv` contained some columns that wouldn't work for this analisis, so the necesary columns were selected, and the columns needed from `SleepDay_merged.csv` were joined into this base table.
```
-- cleaning - selecting necesary rows and converting Minutes to hours to vizualize data in a comprehensive way.

SELECT
 activity.Id,
 activity.ActivityDate,
 TotalSteps,
 TotalDistance,
 VeryActiveMinutes,
 FairlyActiveMinutes,
 LightlyActiveMinutes,
 SedentaryMinutes,
 Calories,
 TotalMinutesAsleep
 FROM
  `bellabeat-395022.Fitabase.daily_activity` AS activity

LEFT JOIN
 `Fitabase.sleep_time` AS sleep
 ON activity.Id = sleep.Id AND activity.ActivityDate = sleep.SleepDay
WHERE Calories != 0 -- rows where calories value = 0 are fully blank. Excluded --
ORDER BY
 Id, ActivityDate
```

### _Pending Table_

LEFT Join was used instead of a Inner Join due to the amount of data. 33 people's Data was collected for `DailyActivity_merged.csv`, but only 24 people's Data was collected for `Sleepday_merged.csv`.
```
-- checking the ammount of users data in each dataset --

SELECT
COUNT(DISTINCT activity.Id) AS DailyActivity_Ids_number,
COUNT(DISTINCT sleep.Id) AS SleepDay_Ids_number

 FROM
  `bellabeat-395022.Fitabase.daily_activity` AS activity

LEFT JOIN
 `Fitabase.sleep_time` AS sleep
 ON activity.Id = sleep.Id AND activity.ActivityDate = sleep.SleepDay
```

### _Pending Table_

# Analysis


