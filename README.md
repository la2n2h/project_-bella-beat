# project_-bella-beat
author: Lan Anh
Date: Jan 01, 2025
# Scenario
 Bellabeat, a high-tech manufacturer of health-focused products for women. Bellabeat is a successful small company, but they have the potential to become a larger player in the
 global smartdevice market. Collecting data on activity, sleep, stress, and reproductive health has allowed Bellabeat to empower women with knowledge about their own health and habits.
### Data analysis:
#### Project goals:
Business task: focus on one of Bellabeat’s products and analyze smart device data to gain insight into how consumers are using their smart devices.
Primary stakeholder: Executive team
Secondary stake holder: Marketing analytics team.
#### Data preparation:
* Data sources: https://www.kaggle.com/datasets/arashnic/fitbit (CC0: Public Domain, dataset made available through Mobius)
* Reliability: This data set contains personal fitness tracker from thirty fitbit users. (CC0: Public Domain, dataset made available through Mobius)
* Original: Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep   monitoring. It includes information about daily activity, steps, and heart rate that can be used to explore users’ habits
* Comprehensive: record physical acitivites, heartrate and sleep mornitoring.
dailyActivities_merged : attribute (Id	ActivityDate	TotalSteps	TotalDistance	TrackerDistance	LoggedActivitiesDistance	VeryActiveDistance	ModeratelyActiveDistance	LightActiveDistance	SedentaryActiveDistance	VeryActiveMinutes	FairlyActiveMinutes	LightlyActiveMinutes	SedentaryMinutes	Calories) 
![image](https://github.com/user-attachments/assets/6323596e-c4c4-495b-922c-f4db5656b7de)
Record time: from Mar 12, 2016 to Apr 12, 2016
user number: 35 users
heartrate_merged: attribute (Id	Time	Value)
![image](https://github.com/user-attachments/assets/27666214-b0da-41bc-b142-a36a8259c7c9)
record time: from Mar 29,2016 to Apr 12, 2016
user number: 13 users
WeightLogInfo_merged: attribute (Id	Date	WeightKg	WeightPounds	Fat	BMI	IsManualReport	LogId)
![image](https://github.com/user-attachments/assets/37451d92-7098-4ace-867e-ff0180dc3b6f)
record time: from Mar 30, 2016 to Apr 12, 206
user number: 11 users
minuteSleep_merged: attribute (Id	date	value	logId)
![image](https://github.com/user-attachments/assets/335f15dd-35cb-480c-8128-50757913d1c1)
record time: from Mar 11, 2016 to Apr 12, 2016
User number: 23 users
* Cited: unknow.
*Limitation of data: the sample size is maximum is 35 users which is quite small so that it may not represent for all population.
The data are not included the age of users which may effect to the result of analysis.
#### Data process: 
Working with dailyActivities_merged data frame.
check null and missing value in Id
```
id_valid <- is.null(dailyActivity_merged$Id) & is.na(dailyActivity_merged$Id)
print(id_valid)
```
check unexpected Id type and format
```
id_format <- !is.character(dailyActivity_merged$Id) | !is.numeric(dailyActivity_merged$Id)
print(id_format)
#### Data analysis:
#### Data analyze
Average daily steps per user
```{R}
avg_steps_per_user <- dailyActivity_merged %>%
  filter (TotalSteps >0) %>%
  group_by (Id) %>%
  summarize (avg_steps = mean(TotalSteps)) %>%
  arrange(desc(avg_steps)) %>%
  print(avg_steps_per_user)
```
Count the number of days each user recorded activity data.

```{r}
days_with_activity <- dailyActivity_merged %>%
  group_by(Id) %>%
  summarize(day_recorded = n()) %>%
  arrange(desc(day_recorded)) %>%
  print(days_with_activity)
```


#Define an "active day" and classify active days for each use
Define "active day"
```{r}
active_levels_df <- data.frame(
Active_Level = c("Highly Active", "Moderately Active", "Lightly Active", "Sedentary"),
  Steps = c("> 20,000", "12,000 - 20,000", "5,000 - 12,000", "< 5,000")
) %>%
print(active_levels_df)
```
Classify active levels
```{r}
# read the data
dailyActivity_merged_df <- read_csv("Fitabase Data 3.12.16-4.11.16/dailyActivity_merged.csv")
active_levels_df <- read_excel("activity_levels.xlsx")
classify_active_levels <- function(row) {
# extract relevant column from row
  steps <- row[which(names(row) == "TotalSteps")]
  
# Define conditions for each activity level
is_highly_active <- (steps > 20000) 
is_moderately_active <- (steps >= 12000 & steps <= 20000)
is_lightly_active <- (steps >= 5000 & steps <= 12000)
is_sedentary <- (steps < 5000)
# assign active level base on conditions
if (is_highly_active) {
    return ('Highly Active')
  } else if (is_moderately_active) {
    return ('Moderately Active')
  } else if (is_lightly_active) {
    return ('Lightly Active')
  } else if (is_sedentary) {
    return ('Sedentary')
  } else {
    return ('NA')  # Handle cases where no condition matches
  }
}
# apply function to each row in the dailyActivity_merged_df
dailyActivity_merged_df$active_level <- apply(dailyActivity_merged_df, 1,classify_active_levels )

# view the result
View(dailyActivity_merged_df)
```

# check trend of active levels from monday to sunday

```{r}
# convert ActivityDate to date format
dailyActivity_merged_df$ActivityDate <- as.Date(dailyActivity_merged_df$ActivityDate, format = "%y-%m-%d")
# create new column day_of_week
dailyActivity_merged_df$day_of_week <- weekdays(dailyActivity_merged_df$ActivityDate)
# calculate the frequency of active levels on each day of week
day_of_week_count <- dailyActivity_merged_df %>%
  group_by(day_of_week, active_level) %>%
  summarize (count= n()) %>%
  ungroup() %>%
  mutate(day_of_week = factor(day_of_week, levels = c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Satuday", "Sunday")))
# create a bar plot
ggplot(day_of_week_count,aes (x= day_of_week, y = count, fill = active_level)) + geom_bar(stat = "identity", position = "dodge") + labs(title = "active level trend by day of week", x = "day_of_week", y = "count")
theme_bw()
# proportions for each day of the week
day_of_week_proportions <- dailyActivity_merged_df %>%
  group_by(day_of_week, active_level)%>%
  summarize(count = n()) %>%
  ungroup () %>%
  group_by(day_of_week)%>%
  mutate(proportions = (count/sum(count)))%>%
  ungroup ()%>%
  mutate(day_of_week = factor(day_of_week, levels = c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Satuday", "Sunday")))
# create stacked bar plot for proportions
ggplot(day_of_week_proportions, aes(x= day_of_week, y = proportions, fill = active_level)) + geom_bar(stat = "identity") + labs (tittle = "Propotion of active levels by day of week", x= "day of week", y = "proportion")
theme_bw()
```

# Analyze the relationship between tracker_distance and total_distance to assess the accuracy and usage of the tracker.
```{R}


```

#Analyze sedentary time to understand user behavior and identify potential areas for improvement.
```{R}
# Calculate average sedentary time per user
avg_sedentary_time <- dailyActivity_merged %>%
  group_by(Id)%>%
  summarize(avg_sedentary_minute = mean(SedentaryMinutes))%>%
print(avg_sedentary_time)
```

```{r}
### Create a histogram of average sedentary time across users

ggplot(avg_sedentary_time, aes(x= avg_sedentary_minute))+
  geom_histogram(binwidth = 30, fill ="lightblue", color = "black")+
  labs(title = "Distribution of Average Sedentary Time Across Users", x = "Average Sedentary Minutes", y = "Number of Users") +
  theme_bw()

```
# Calculate daily and weekly sedentary time trends for each user
```{R}
user_daily_sedentary <- dailyActivity_merged %>%
  group_by(Id,ActivityDate)%>%
  summarize(daily_sedentary_minutes = sum(SedentaryMinutes))

user_weeky_sedentary <- user_daily_sedentary %>%
  mutate(week= week(ActivityDate))%>%
  group_by(Id, week)%>%
  summarize(weekly_sendetary_minute = sum(daily_sedentary_minutes))%>%
  
  print(user_weeky_sedentary)
```


Visualization

Create informative visualizations:
  Line plots: Track daily activity trends for individual users or user groups.
Bar charts: Compare average activity levels across different user segments.
Heatmaps: Visualize activity patterns across different times of the day or days of the week.
