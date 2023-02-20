## Google Data Analytics Capstone Project

###### This 'Capstone Project' has been completed to meet the [Google Data Analytics Professional Certificate - Coursera](https://www.coursera.org/professional-certificates/google-data-analytics) requirements.

### Case Study 2: How Can a Wellness Technology Company Play It Smart?
###### Bellabeat Fitness Data Analysis

---
Author: Flavio Ribeiro Córdula  

[![text](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/flavio-ribeiro-córdula-632a19234)

Start date: 09/02/2023

End date: XX/XX/XXXX

---

### Case Study info:

- Bellabeat, is a high-tech manufacturer of health-focused products for women. Urška Sršen, cofounder and Chief Creative Officer of Bellabeat, believes that analyzing smart device fitness data could help unlock new growth opportunities for the company.

- In a nutshel (and assuming I work for Bellabeat), the task here is to identify consumption patterns in users of FitBit products (a competitor business) that can support the Bellabeat's Marketing strategy. 

- This Case Study follows the six data analysis phases designed by Google and makes use of PostgreSQL and Tableau.

### Phase 1: ASK

- The Company

	- Bellabeat is a high-tech company that manufactures health-focused smart products. Collecting data on activity, sleep, stress, and reproductive health has allowed Bellabeat to empower women with knowledge about their own health and habits. Since it was founded in 2013, Bellabeat has grown rapidly and quickly positioned itself as a tech-driven wellness company for women. By 2016, Bellabeat had opened offices around the world and launched multiple products.

- Stakeholders
	
	- Primary stakeholders: Urška Sršen and Sando Mur, executive team members.

	- Secondary stakeholders: Bellabeat marketing analytics team.

- Products
	
	- **Bellabeat app**: The Bellabeat app provides users with health data related to their activity, sleep, stress, menstrual cycle, and mindfulness habits. This data can help users better understand their current habits and make healthy decisions. The Bellabeat app connects to their line of smart wellness products.
	
	- **Leaf**: Bellabeat’s classic wellness tracker can be worn as a bracelet, necklace, or clip. The Leaf tracker connects to the Bellabeat app to track activity, sleep, and stress.
	
	- **Time**: This wellness watch combines the timeless look of a classic timepiece with smart technology to track user activity, sleep, and stress. The Time watch connects to the Bellabeat app to provide you with insights into your daily wellness.

	- **Spring**: This is a water bottle that tracks daily water intake using smart technology to ensure that you are appropriately hydrated throughout the day. The Spring bottle connects to the Bellabeat app to track your hydration levels.
	
	- **Bellabeat membership**: Bellabeat also offers a subscription-based membership program for users.
Membership gives users 24/7 access to fully personalized guidance on nutrition, activity, sleep, health and beauty, and mindfulness based on their lifestyle and goals. 

- Business Task:

	- Analyze FitBit Fitness Tracker Data to obtain insights and help guide marketing strategy for Bellabeat to become a larger player in the global smart device market.

- Questions:
	- Question 1
	- Question 2
	- ... ?????????????????????????????????????????????
 
### Phase 2: PREPARE

- Dataset Credibility was verified by checking the information on the website:

	- The dataset used in this Case Study can be accessed via [Kaggle](https://www.kaggle.com/datasets/arashnic/fitbit) and corresponds to data of FitBit Fitness Tracker (a smartwatch) with personal fitness information such heart rate, calories, etc.

	- Title: FitBit Fitness Tracker Data
	- Subtitle: Pattern recognition with tracker data: Improve Your Overall Health

	- Content
		- This dataset generated by respondents to a distributed survey via Amazon Mechanical Turk between 03.12.2016-05.12.2016. Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. Individual reports can be parsed by export session ID (column A) or timestamp (column B). Variation between output represents use of different types of Fitbit trackers and individual tracking behaviors / preferences.

	- Tags: business and exercise 

	- Files
		- 18 csv files 

	- Metadata
		- Even though there is some metadata for the dataset, the metadata for the actual files are missing. Some columns are difficult to infer.

	- Updated
		- Last update: Dec/2020

	- License specified: CC0: Public Domain

	- Limitations:
		- The data is outdated
			- Collected in 2016
		- Small sample size
			- Only 30 distinct participants
		- Small time frame
			- Data was collected between 03.12.2016-05.12.2016
			- Approximately one month worth of data
		- No demographic information
			- age, gender, profession, location
		
## Phase 3: PROCESS

- I have chosen to use PostgreSQL and Tableau for this case study. PostgreSQL will be used for cleaning/transforming and data analysis while tableau will be used to build visualizations/dashboards for the stakeholder.

- Loading the data (18 csv files)
	- all the 18 csv files were loaded into a PostgreSQL database using DBeaver, a SQL client software application and a database administration tool, via the “Import Data” function. The two adjustments I had to do while performing this data transfer was to change the column delimiter to a comma ( , ) and to configure the target type for the id column to int8.
	
![alt text](https://github.com/flaviocordula/Google_Data_Analytics_Capstone_Project-Case_Study_2-Bellabeat/blob/main/imagens/loaded_data_dbeaver.png?raw=true)

- Getting familiar with the tables and data (see [SQL file](sql/getting_familiar_with_the_data.sql))
	1. Check for count of distinct participants 
		- There are 33 different ids. Either the content (description on Kaggle) is wrong, or some participants wore more than one smartwatch.		
			- I considered here 33 different users (participants) 
		- Most tables contain 33 distinct users, except for heartrate_seconds_merged with 14 and minutesleep_merged and sleepday_merged with 24. 
			- From this very simple verification, one can infer that only 14 users monitored their heartrate data and 24 (out of 33) actually used their smartwatch while sleeping.
	2. Select tables
		- Discart minute-level tables. To much detail for the first case study version!
		- Discart dailycalories_merged, dailyintensities_merged and dailysteps_merged.
			- Their data are all included in the dailyactivity_merged table. 
		- So, the tables to be cleaned and used on the next phases are:
			- dailyactivity_merged
			- heartrate_seconds_merged
			- hourlycalories_merged
			- hourlyintensities_merged
			- hourlysteps_merged
			- sleepday_merged
			- weightloginfo_merged		

- Cleaning the data from selected tables (see [SQL cleaning file](sql/cleaning.sql))
	- From the seven selected tables to be cleaned, the first table, dailyactivity_merged, did not need any cleaning. The other six tables needed cleaning and their timestamp columns were split into date ('mm/dd/yyyy'), day of the week and time ('hh24:mi:ss'). 
	- The following sql code does the cleaning for the table heartrate_seconds_merged. For the full sql file, see [SQL cleaning file](sql/cleaning.sql).   

```sql
ALTER TABLE case_study.heartrate_seconds_merged 
ADD COLUMN heartrate_date date,
ADD COLUMN heartrate_day_name varchar (15),
ADD COLUMN heartrate_time varchar(8); 

UPDATE case_study.heartrate_seconds_merged hsm 
SET heartrate_date = to_date(hsm."Time",'mm/dd/yyyy')
WHERE hsm."Time" IS NOT NULL;

UPDATE case_study.heartrate_seconds_merged hsm 
SET heartrate_day_name = to_char(heartrate_date, 'day')
WHERE hsm.heartrate_date IS NOT NULL;

UPDATE case_study.heartrate_seconds_merged hsm 
SET heartrate_time = to_char(to_timestamp(hsm."Time",'mm/dd/yyyy hh12:mi:ss AM,PM'),'hh24:mi:ss')
WHERE hsm."Time" IS NOT NULL;
```

	- Trim to remove spaces on day_name columns
	
```sql 
UPDATE 	case_study.heartrate_seconds_merged
SET 	heartrate_day_name = trim(heartrate_day_name)
WHERE 	heartrate_day_name IS NOT NULL;

UPDATE 	case_study.hourlycalories_merged 
SET 	activity_day_name = trim(activity_day_name)
WHERE 	activity_day_name IS NOT NULL;

UPDATE 	case_study.hourlyintensities_merged  
SET 	activity_day_name = trim(activity_day_name)
WHERE 	activity_day_name IS NOT NULL;

UPDATE 	case_study.hourlysteps_merged  
SET 	activity_day_name = trim(activity_day_name)
WHERE 	activity_day_name IS NOT NULL;

UPDATE 	case_study.sleepday_merged  
SET 	sleep_day_name = trim(sleep_day_name)
WHERE 	sleep_day_name IS NOT NULL;

UPDATE 	case_study.weightloginfo_merged 
SET 	weightlog_day_name = trim(weightlog_day_name)
WHERE 	weightlog_day_name IS NOT NULL;	
```
	- The last cleaning made merged the three hourly tables (hourlycalories_merged, hourlyintensities_merged and hourlysteps_merged) into hourlyactivity_merged (new merged table).
		- See [SQL cleaning file](sql/cleaning.sql) for more details.

```sql
CREATE TABLE case_study.hourlyactivity_merged AS ( 
SELECT	hm.id, hm.activity_date, hm.activity_day_name, hm.activity_hour, hm.calories, hm2.totalintensity, hm2.averageintensity, hm3.steptotal  
FROM 	case_study.hourlycalories_merged hm 
		INNER JOIN case_study.hourlyintensities_merged hm2 ON (hm2.id = hm.id AND hm2.activityhour = hm.activityhour)
		INNER JOIN case_study.hourlysteps_merged hm3 ON (hm3.id = hm.id AND hm3.activityhour = hm.activityhour)
ORDER BY hm.id, hm.activity_date, hm.activity_hour);
```

## Phase 4: ANALYSE
	
