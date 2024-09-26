# Aviation Accident Analysis
## Table of contents
- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Tools](#tools)
- [Data cleaning](#data-cleaning)
- [Exploratory Data Analyis](#exploratory-data-analysis)
- [Data Analyis](#data-analysis)
- [Dashboard Presentation](#dashboard-presentation)
- [Results and Findings](#results-and-findings)
- [Recommendations](#recommendations)


## Project Overview
This analysis aims to identify low-risk aircraft based on accident incidents to guide an organization's decision on purchasing and operating airplanes for both commercial and private enterprises.

## Data Source
The data used for this analysis was from [kaggle dataset](https://www.kaggle.com/datasets/khsamaha/aviation-accident-database-synopses).

## Tools
- Python for data cleaning and analyis.
- Tableau for creating a report dashboard.

## Data cleaning
The initial dataset had a shape of 88889, 31 i.e.88889 rows and 31 columns. However, it resulted into 22 columns after cleaning.
The cleaning entailed:
1. Dropping columns with relatively high missing values. That is more than 50% missing values of the overall data.
2. Imputing missing values with median for numerical data and mode for the categorical data.
3. Extracting Year, Month and Day into seperate column each from the event date column, and also changing the event date datatype into date.
4. Harmonizing column names which had repetition and  upper/lower case mix up in naming for elements in each column.

## Exploratory Data Analysis
The analysis involved answering key questions which entailed:
- Identifying the aircrafts with least accidents.
- Identifying aircrafts with the highest record of uninjured passengers among the aircrafts with least accidents.
- Level of fatalities if aircraft is amateur built.
- Identifying the low risk aircraft based on model, ameteur built and engine type.
- Relating purpose of flight to the number of accidents.
- Identifying the most recommendable month for travel.

## Data Analysis
### 1. Identifying the aircrafts with least accidents.
```python

#I first group the make column by the count of accident number to represesent the occurence of each accident
# Confirming Makes with least number of accidents
accidents_by_make_least = df.groupby('Make')['Accident.Number'].count().reset_index()
accidents_by_make_least = accidents_by_make_least.sort_values(by='Accident.Number', ascending=False)
accidents_by_make_least.tail(10)
#Graph with the least accident cases
plt.figure(figsize=(12,6))
sns.barplot(data=accidents_by_make_least.tail(10), x='Make', y='Accident.Number')
plt.title('Manufacturers with the least Number of Accidents')
plt.xlabel('Manufacturer Make')
plt.ylabel('Number of Accidents')
plt.xticks(rotation=80)
plt.tight_layout()
plt.show();
```
![image](https://github.com/user-attachments/assets/6f5cef24-c008-495c-9401-29ed29e3faa5)

### 2. Identifying aircrafts with the highest record of uninjured passengers among the aircrafts with least accidents.
```python
# Group by 'Make' and count the number of accidents
accidents_by_make = df.groupby('Make')['Accident.Number'].count().reset_index(name='Total_Accidents')

# Sort and get the 10 Make manufacturers with the least accidents
accidents_by_make_least = accidents_by_make.sort_values(by='Total_Accidents', ascending=False)
least_10_makes = accidents_by_make_least.tail(10)['Make']

# Filter the dataset for those least accident-prone manufacturers
df_filtered_least = df[df['Make'].isin(least_10_makes)]

# Combine the injury columns
injuries = ['Total.Fatal.Injuries', 'Total.Serious.Injuries', 'Total.Minor.Injuries', 'Total.Uninjured']

# Group by 'Make' and sum up the injuries for each make
injuries_by_make_least = df_filtered_least.groupby('Make')[injuries].sum().reset_index()

# Set the 'Make' column as the index for plotting
injuries_by_make_least.set_index('Make', inplace=True)

# Create a stacked bar plot
ax = injuries_by_make_least.plot(kind='bar', stacked=True, figsize=(12, 8), colormap='viridis')

# Customize the plot
plt.title('Injury Levels by Aircraft Make (Least Accidents)')
plt.xlabel('Aircraft Make')
plt.ylabel('Number of Injuries')
plt.xticks(rotation=45)
plt.legend(title='Type of Injury', bbox_to_anchor=(1.05, 1), loc='upper left')
plt.tight_layout()

# Show the plot
plt.show();
```
![image](https://github.com/user-attachments/assets/8c198bb9-4ad5-4c4b-af7b-105394f5de3a)

### 3.  Level of fatalities if aircraft is amateur built.
```python
#Grouping by 'Amateur.Built' and counting injury severity
df.groupby('Amateur.Built')['Injury.Severity'].value_counts(normalize = True).unstack('Injury.Severity').plot.bar(stacked = True)

#Formatting
plt.xticks(rotation = 0)
plt.title('Fatality rate if built by amateur')
plt.xlabel('')
plt.legend(title = "Injury Severity",bbox_to_anchor=(1.4,1),loc='upper right') #The loc specifies the location of the legend in the plot
plt.show();
```
![image](https://github.com/user-attachments/assets/dc76754a-3716-456d-8d74-c718652b59d5)

### 4.Identifying the low risk aircraft based on model, ameteur built and engine type.
```python
#Plotting engine type and makes with least incident of accidents
# Group by 'Make' and count the number of accidents
accidents_by_make = df.groupby('Make')['Accident.Number'].count().reset_index(name='Total_Accidents')

# Sort and get the 10 Make manufacturers with the least accidents
accidents_by_make_least = accidents_by_make.sort_values(by='Total_Accidents', ascending=False)
least_10_makes = accidents_by_make_least.tail(10)['Make']

# Filter the dataset for those least accident-prone manufacturers
df_filtered_least = df[df['Make'].isin(least_10_makes)]

# Group by 'Make' and 'Engine.Type' to count the number of accidents for each combination
accidents_by_engine_type = df_filtered_least.groupby(['Make', 'Engine.Type'])['Accident.Number'].count().unstack(fill_value=0)

# Plot a bar chart
ax = accidents_by_engine_type.plot(kind='bar', figsize=(12, 8), colormap='viridis')

# Customize the plot
plt.title('Accidents by Aircraft Make and Engine Type (Least Accidents)')
plt.xlabel('Aircraft Make')
plt.ylabel('Number of Accidents')
plt.xticks(rotation=45)
plt.legend(title='Engine Type', bbox_to_anchor=(1.05, 1), loc='upper left')
plt.tight_layout()
plt.show();
```
![image](https://github.com/user-attachments/assets/942754b0-9df8-4fa1-88b9-f0c0ab061bfb)

### 5. Relating purpose of flight to the number of accidents.
```python
#This specific analysis will help to identify safety measures and which aircrafts are most risky in terms of commercial or personal purposes.
# This will also shed light on safety measures for the different aircrafts.
# The code will plot fatal injuries against purpose of flight
injuries=df.groupby('Purpose.of.flight')['Total.Fatal.Injuries'].count().reset_index()
injuries =injuries.sort_values(by='Total.Fatal.Injuries', ascending=False)
sns.barplot(data=injuries.head(12),x='Purpose.of.flight',y='Total.Fatal.Injuries')
plt.title('Level of fatal injuries by purpose of flight')
plt.xlabel('Purpose of flight')
plt.ylabel('Total fatal injuries')
plt.xticks(rotation=80)
plt.tight_layout()
plt.show();
```
![image](https://github.com/user-attachments/assets/ed3ab239-27b2-4210-b359-a75e943bc604)

### 6.Identifying the most recommendable month for travel.
```python
# Group by month name and count the number of accidents
accidents_per_month = df.groupby('month_name')['Event.Id'].count().reset_index()

# Sort by month order
month_order = ['January', 'February', 'March', 'April', 'May', 
               'June', 'July', 'August', 'September', 'October', 
               'November', 'December']
accidents_per_month['month_name'] = pd.Categorical(accidents_per_month['month_name'], 
                                                    categories=month_order, 
                                                    ordered=True)
accidents_per_month = accidents_per_month.sort_values('month_name')

# Plotting
plt.figure(figsize=(12, 6))
sns.barplot(x='month_name', y='Event.Id', data=accidents_per_month, palette='viridis')
plt.title('Number of Accidents per Month')
plt.xlabel('Month')
plt.ylabel('Number of Accidents')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show();
```
![image](https://github.com/user-attachments/assets/7acd48ff-d88e-411a-896e-88ff1d12ce66)

## Dashboard presentation
This is a screenshot of the overall report dashboard for the analysis done in Tableau. [Here is the link to the Dashboard](https://public.tableau.com/app/profile/winny5092/viz/InteractivedashboardonAircraftAnalysis/DashboardonAircraftAnalysis?publish=yes).

![image](https://github.com/user-attachments/assets/9b1a3f90-e68d-4d9b-8150-999ff64bd2d8)


## Results and Findings
1. The Flight Makes with least number of flight accidents entailed; Grigg/bowers,Griffith-boyd,Griffin-Thomas,Grieme,Grice,Gribosh,Greth,Gregg and Greg Smith.

2. Out of the 10 flight makes with least incidents, the ones which had higher records of uninjured passengers onboard entailed;Grice,Griffith-boyd and Greth.

3. Out of the 3 makes, Griffith-boyd and Greth presented to have higher records of uninjured passengers compared to Grice.

4. Further, the 2 top Makes with highest number of uninjured passengers were compared based on Ameteur Built, Model and Engine Type. Both Griffith-boyd and Greth presented to have 1     model each and were all Ameteur Built. However, Greth did not have a defined engine type, therefore it was ruled out.

5. With that,Griffith-boyd presented to be the most reliable and less risky aircraft model since it had a defined engine type by the name 'reciprocating'. Having a specific engine type would translate that there is a better track records of safety providing an indication of the overall quality of the flight experience.

6. In terms of overall analysis, executive/corporate flights presented to have fewer accidents. With personal flights leading with the highest accident incidents, executive/corporate appeared 12th in the rank with least incidents.

7. Lastly, summer seasons(June, July, August) presented to have higher incidents of flight accidents compared to winter seasons(November, December, January).

## Recommendations
1. It is recommandable that the company considers Griffith-boyd Make as the most reliable and safe aircraft.
2. In terms of operations, the company can opt for executive/corporate flight business compared to personal flight business.
3. Lastly, once in operation, the company can limit the number of flights in operation during summer seasons or implement more protective gears during that season to counter the chances of the many accidents that occur during that period.





Tableau dashboard [https://public.tableau.com/app/profile/winny5092/viz/InteractivedashboardonAircraftAnalysis/DashboardonAircraftAnalysis?publish=yes]
