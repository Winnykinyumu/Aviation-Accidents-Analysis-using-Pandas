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


Tableau dashboard [https://public.tableau.com/app/profile/winny5092/viz/InteractivedashboardonAircraftAnalysis/DashboardonAircraftAnalysis?publish=yes]
