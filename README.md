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
#### Data analysis
Count the number of days each user recorded activity data.



