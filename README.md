# Bellabeat-Case-Study
Case Study from Google Data Analytics professional certificate with Coursera

# Introduction
This project has been performed for Bellabeat, a high-tech company that manufactures health-focused smart products.
Using technology that informs and inspires women around the world. Collecting data on activity, sleep, stress, and reproductive health has allowed Bellabeat to empower
women with knowledge about their own health and habits. Since it was founded in 2013, Bellabeat has grown rapidly and
quickly positioned itself as a tech-driven wellness company for women.


They would like to analyze Bellabeat’s available consumer data to reveal more growth opportunities. As they want to focus on how people are already using their smart devices to identify trends that can help Bellabeat's marketing strategy.

## Questions for the Analysis:
1. What are some trends in smart device usage?
2. How could these trends apply to Bellabeat customers?
3. How could these trends help influence Bellabeat's marketing strategy?

The Business task this time is to identify opportunities for improvement and growth based on trends in smart device usage and user habits.

# Preparing Data

For this analysis, I've been provided with a Fitbit personal fitness tracker dataset, available on [Kaggle](https://www.kaggle.com/datasets/arashnic/fitbit).
Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes information
about daily activity, steps, and heart rate.

The dataset contains a small sample since only 30 users' data was collected; additionally, the data was collected in 2016, so it is not updated.

# Processing data

The Data files considered were `DailyActivity_merged.csv` and `SleepDay_merged.csv`. Initially `weightLogInfo_merged.csv` was also considered but was discarded later since it contains data for only 8 users.

### Software used
SQL was the tool used to clean, transform and merge the data, through BigQuery, from Google. 
When trying to import the datasets a problem was encountered: File `Sleepday_merged.csv` Date format 'DD/MM/YYYY hh:mm:ss _tt_' was not readable by SQL. So I converted the date to DD/MM/YYYY only.
While converting files, no missing values were encountered. 

### Cleaning

Dataset `DailyActivity_merged.csv` contained some columns that wouldn't work for this analysis, so the necessary columns were selected, and the columns needed from `SleepDay_merged.csv` were joined into this base table.
```
-- cleaning - selecting necessary rows and converting Minutes to hours to visualize data comprehensively.

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

![Preview of the output table from SQL. Limited to 7 rows](/Base_table.png)

LEFT JOIN was used instead of an INNER JOIN to include all the User's data gathered. Since 33 people's Data was collected for `DailyActivity_merged.csv`, but only 24 people's Data was collected for `Sleepday_merged.csv`.
```
-- checking the amount of users data in each dataset --

SELECT
COUNT(DISTINCT activity.Id) AS DailyActivity_Ids_number,
COUNT(DISTINCT sleep.Id) AS SleepDay_Ids_number

 FROM
  `bellabeat-395022.Fitabase.daily_activity` AS activity

LEFT JOIN
 `Fitabase.sleep_time` AS sleep
 ON activity.Id = sleep.Id AND activity.ActivityDate = sleep.SleepDay
```

![Preview of the output table from SQL](/Num_users.png)

# Analysis

I checked the Minimum, maximum, and average values of the columns of interest. Using JOIN as well to have all the data in the same output table.

```
-- total time - Query to calculate minimum, maximum, and average of the total time spent using multiple (2) tables

SELECT
  MIN(TotalSteps) AS Min_steps,
 MAX(TotalSteps) AS Max_steps,
 ROUND(AVG(TotalSteps), 2) AS Average_steps, -- Using round to avoid excessive decimals -
 MIN(SedentaryMinutes) AS Min_sedentary_time,
 MAX(SedentaryMinutes) AS Max_sedentary_time,
 ROUND(AVG(SedentaryMinutes), 2) AS Average_sedentary_time,
 MIN(VeryActiveMinutes) AS Min_vactive_time,
 MAX(VeryActiveMinutes) AS Max_vactive_time,
 ROUND(AVG(VeryActiveMinutes), 2) AS Average_vactive_time,
 MIN(FairlyActiveMinutes) AS Min_fairactive_time,
 MAX(FairlyActiveMinutes) AS Max_fairactive_time,
 ROUND(AVG(FairlyActiveMinutes), 2) AS Average_fairactive_time,
 MIN(LightlyActiveMinutes) AS Min_lightactive_time,
 MAX(LightlyActiveMinutes) AS Max_lightactive_time,
 ROUND(AVG(LightlyActiveMinutes), 2) AS Average_lightactive_time,
 MIN(Calories) AS Min_calories,
 MAX(Calories) AS Max_calories,
 ROUND(AVG(Calories), 2) AS Average_calories,
  MIN(TotalMinutesAsleep) AS Min_sleep,
 MAX(TotalMinutesAsleep) AS Max_sleep,
 ROUND(AVG(TotalMinutesAsleep), 2) AS Average_sleep
FROM
 `bellabeat-395022.Fitabase.daily_activity` AS activity
LEFT JOIN `Fitabase.sleep_time` AS sleep
 ON activity.Id = sleep.Id -- merging data from the sleep_time table into this one to facilitate visualization later --
WHERE TotalSteps !=0
-- In normal circumstances couldn't be possible to have 0 total steps throughout an entire day, and have a
0 value will impact the data Min and Avg calculations. '0' Excluded to have a better approximation -- 

```
| Min_steps | Max_steps | Average_steps  |Min_sedentary_time | Max_sedentary_time | Average_sedentary_time |	Min_vactive_time	| Max_vactive_time	| Average_vactive_time	| Min_fairactive_time | Max_fairactive_time | Average_fairactive_time | Min_lightactive_time | Max_lightactive_time | Average_lightactive_time | Min_calories | Max_calories | Average_calories |	Min_sleep	| Max_sleep	| Average_sleep	| 
|-----------|-----------|----------------|-------------------|--------------------|------------------------|------------------|------------------|----------------------|---------------------|---------------------|-------------------------|----------------------|----------------------|--------------------------|--------------|--------------|------------------|-----------|-----------|---------------|
| 4         | 36019     | 8541.85        | 0                 | 1440               | 775.64                 | 0                | 210              | 25.15                | 0                   | 143                 | 18.12                   | 0                    | 518                  | 210.55                   | 52           | 4900         | 2364.57          | 58        | 796       | 419.11        |


Further checking these averages some Tableu visualizations were made. 

![Tableu visualization no 1. Bar chart of average time spent](/Avg_time_comp.png) _change to comp2_

The visualization above confirms what our base table showed, the average sedentary time spent is much bigger than the average active time spent. 


![Tableu visualization no 1. Bar chart of average time spent](/Daily_comparison.png)

For that reason, I checked which days tend to have the most average sedentary time. It's visible that Tuesday is the day with the most sedentary time, followed by Wednesday, and Thursday, respectively. While the least sedentary time was recorded on the weekends.

![Tableu visualization no 1. Bar chart of average time spent](/Sleep_comp.png) 

Doing some deeper research a chart comparing the average time slept and average sedentary time, having a better perspective of the amount of inactive time. And is also appreciated the irregularity of the sleeping hours, despite the irregularity, the average has been maintained in a good sleeping time, since the recommendation is to sleep between 6-8 hours(360-480 min).

![Tableu visualization no 1. Bar chart of average time spent](/Calories_correlation.png)

It's visible how the Steps walked and calories burnt have a positive correlation, and it's also good to see how was a slight increase in the average Calories burnt thorough the period tested. That could be attributed to the health app track and/or suggestions

You can take a look at the detailed tableau dashboard [here](https://public.tableau.com/app/profile/ramses.rhenals/viz/Bellabeat_16913877065250/Dashboard4)!

## Conclusions

* Users did well with the total steps walked, the average was 8541.85 steps, which is close to the [10,000 steps recommended](https://www.medicalnewstoday.com/articles/how-many-steps-should-you-take-a-day#:~:text=Current%20guidelines%20suggest%20that%20most,in%20line%20with%20physical%20activity.) to achieve health benefits.
* Users showed a clear and worrying habit of doing sedentary activities most of the time, and a lack of time spent on moderate or/and highly active activities.
* The days with the most sedentarism trend were in the middle of the week, which could be related to the Job's daily tasks.
* Users seem to worry about their sleeping time, but the habit does not seem too strong and there is a chance of improvement in their sleeping time consistency.

# Recommendations

