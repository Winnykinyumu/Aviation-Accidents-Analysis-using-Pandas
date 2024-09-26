# Aviation Accident Analysis

## Project Overview
This analysis aims to identify low-risk aircraft based on accident incidents to guide an organization's decision on purchasing and operating airplanes for both commercial and private enterprises.

## Data Source
The data used for this analysis was from [kaggle dataset](https://www.kaggle.com/datasets/khsamaha/aviation-accident-database-synopses).

## Tools
- Python for data cleaning and analyis.
- Tableau for creating a report dashboard.

## Data cleaning/preparation
The initial dataset had a shape of 88889, 31 i.e.88889 rows and 31 columns. However, it resulted into 22 columns after cleaning.
The cleaning entailed:
1. Dropping columns with relatively high missing values. That is more than 50% missing values of the overall data.
2. Imputing missing values with median for numerical data and mode for the categorical data.
3. Extracting Year, Month and Day into seperate column each from the event date column, and also changing the event date datatype into date.
4. Harmonizing column names which had repetition and  upper/lower case mix up in naming for elements in each column.

## Exploratory Data Analysis
The analysis involved answering key questions which entailed:
- Identifying the aircrafts with least accidents.
- identifying aircrafts with the highest record of uninjured passengers among the aircrafts with least accidents.
- Choosing the low risk aircraft based on model, ameteur built and engine type.
- Relating purpose of flight to the number of accidents.
- Identifying the most recommendable month for travel.

## Data Analysis
### 1. Identifying the aircrafts with least accidents.



Tableau dashboard [https://public.tableau.com/app/profile/winny5092/viz/InteractivedashboardonAircraftAnalysis/DashboardonAircraftAnalysis?publish=yes]
