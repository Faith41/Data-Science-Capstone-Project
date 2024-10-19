# Data-Science-Capstone-Project
Problem Statement
1. Collect weather data from OpenWeatherMap API and Weather API for different regions in Kenya
2. Store it in a database of your choice (relational or non-relational)
3. Analyze weather patterns using Python.
The goal is to derive insights into temperature trends, rainfall patterns, and other weather variables across different regions.These insights can help users make informed decisions for weather-sensitive activities, such as agriculture, tourism, and outdoor events.
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import requests
cities={‘Nairobi’,’Nakuru’,’Kisumu’,’Mombasa’,’Eldoret’,’Kiambu’,’Nyeri’,’Nanyuki’,’Muranga’,’Kericho’,’Bomet’,’Kisii’,’Nyamira’,’Migori’,’Kakamega’,’Bungoma’,’Kilifi’}
api_key=’Your Api Key here'
base_url=’https://api.openweathermap.org/data/2.5/weather'

def get_weather_data(city):
url=f’{base_url}?q={city}&appid={api_key}&units=metric’
response=requests.get(url)

if response.status_code==200:
data=response.json()
return data
else:
print(f’Failed to get data for {city}’)
return None
def collect_data(cities):
weather_data_list = []
for city in cities:
data=get_weather_data(city)
if data:
weather_info={
‘date’:data[‘dt’],
‘city’:data[‘name’],
‘longitude’:data[‘coord’][‘lon’],
‘latitude’:data[‘coord’][‘lat’],
‘temperature’:data[‘main’][‘temp’],
‘feel_like’:data[‘main’][‘feels_like’],
‘max_temperature’:data[‘main’][‘temp_max’],
‘min_temperature’:data[‘main’][‘temp_min’],
‘humidity’:data[‘main’][‘humidity’],
‘pressure’:data[‘main’][‘pressure’],
‘wind_speed’:data[‘wind’][‘speed’],
‘visibility’:data[‘visibility’],
‘rain’:data[‘rain’][‘1h’] if ‘rain’ in data and ‘1h’ in data[‘rain’] else 0,
‘clouds’:data[‘clouds’][‘all’],
‘weather_condition’:data[‘weather’][0][‘main’],
‘weather_description’:data[‘weather’][0][‘description’],
}

weather_data_list.append(weather_info)

if weather_data_list:
csv_file=’weather_data.csv’
df=pd.DataFrame(weather_data_list)

df.to_csv(csv_file,header=True,index=False)

collect_data(cities)
# Check for missing values
print(df.isnull().sum())

# Get summary statistics for numeric columns
print(df.describe())

# Fill missing values (if any)
df[‘rain’].fillna(0, inplace=True) # assuming missing rain data can be replaced with 0

# Drop rows with missing critical data
df.dropna(subset=[‘temperature’, ‘humidity’], inplace=True)

# Analyze: Average Rainfall by City
avg_rainfall = df.groupby(‘city’)[‘rain’].mean()
print(“Average Rainfall by City:”)
print(avg_rainfall)

# Analyze: Average Temperature by City
avg_temp = df.groupby(‘city’)[‘temperature’].mean()
print(“Average Temperature by City:”)
print(avg_temp)

import matplotlib.pyplot as plt

# Plot average temperature by city
avg_temp.plot(kind=’bar’, color=’blue’)
plt.title(‘Average Temperature by City’)
plt.xlabel(‘City’)
plt.ylabel(‘Temperature (°C)’)
plt.show()

# Plot average rainfall by city
avg_rainfall.plot(kind=’bar’, color=’green’)
plt.title(‘Average Rainfall by City’)
plt.xlabel(‘City’)
plt.ylabel(‘Rainfall (mm)’)
plt.show()

# Convert ‘date’ column to datetime
df[‘date’] = pd.to_datetime(df[‘date’])

# Group by date to analyze temperature trends over time
temp_trends = df.groupby(‘date’)[‘temperature’].mean()

# Plot temperature trends
temp_trends.plot(kind=’line’)
plt.title(‘Temperature Trends Over Time’)
plt.xlabel(‘Date’)
plt.ylabel(‘Average Temperature (°C)’)
plt.show()

# Print the column names
print(df.columns)

avg_humidity = df.groupby(‘city’)[‘humidity’].mean()
print(“Average Humidity by City:”)
print(avg_humidity)

# Select relevant columns for analysis
weather_factors = df[[‘temperature’, ‘humidity’, ‘rain’, ‘wind_speed’]]

# Correlation matrix
correlation_matrix = weather_factors.corr()

# Display the correlation matrix
print(correlation_matrix)

# Visualize the correlation matrix using a heatmap
import seaborn as sns
import matplotlib.pyplot as plt

plt.figure(figsize=(8, 6))
sns.heatmap(correlation_matrix, annot=True, cmap=’coolwarm’, linewidths=0.5)
plt.title(‘Correlation Matrix of Weather Factors’)
plt.show()

pip install seaborn

# Select relevant columns for analysis
weather_factors = df[[‘temperature’, ‘humidity’, ‘rain’, ‘wind_speed’]]

# Correlation matrix
correlation_matrix = weather_factors.corr()

# Display the correlation matrix
print(correlation_matrix)

# Visualize the correlation matrix using a heatmap
import seaborn as sns
import matplotlib.pyplot as plt

plt.figure(figsize=(8, 6))
sns.heatmap(correlation_matrix, annot=True, cmap=’coolwarm’, linewidths=0.5)
plt.title(‘Correlation Matrix of Weather Factors’)
plt.show()

# Summary statistics for each weather factor
summary_stats = df[[‘temperature’, ‘humidity’, ‘rain’, ‘wind_speed’]].describe()
print(summary_stats)

# Average values by city for multiple factors
avg_factors = df.groupby(‘city’)[[‘temperature’, ‘humidity’, ‘rain’, ‘wind_speed’]].mean()

# Plot the average values for each factor by city
avg_factors.plot(kind=’bar’, figsize=(10, 6), subplots=True, layout=(2, 2), sharex=True)
plt.suptitle(‘Average Weather Factors by City’)
plt.show()

# Visualize relationships between multiple factors
import seaborn as sns

sns.pairplot(df[[‘temperature’, ‘humidity’, ‘rain’, ‘wind_speed’]])
plt.suptitle(‘Pairplot of Weather Factors’, y=1.02)
plt.show()

date 0 city 0 longitude 0 latitude 0 temperature 0 feel_like 0 max_temperature 0 min_temperature 0 humidity 0 pressure 0 wind_speed 0 visibility 0 rain 0 clouds 0 weather_condition 0 weather_description 0 dtype: int64 date longitude latitude temperature \ count 17 17.000000 17.000000 17.000000 mean 1970–01–01 00:00:01.729331905 36.132582 -0.811682 25.369412 min 1970–01–01 00:00:01.729331729 34.473100 -4.054700 21.490000 25% 1970–01–01 00:00:01.729331732 34.761700 -1.063400 23.150000 50% 1970–01–01 00:00:01.729331736 35.341600 -0.631800 24.400000 75% 1970–01–01 00:00:01.729332152 36.947600 -0.102200 27.160000 max 1970–01–01 00:00:01.729332333 39.849900 0.563500 30.720000

…

50% 2.360000 10000.000000 0.300000 54.000000 75% 3.110000 10000.000000 1.260000 62.000000 max 5.940000 10000.000000 1.580000 86.000000 std 1.466542 740.419213 0.611908 14.237998

Average Rainfall by City: city Bungoma 0.40 Eldoret 1.34 Kakamega 0.00 Kericho 0.30 Kiambu 1.58 Kilifi 0.00 Kisii 0.00 Kisumu 0.00 Migori 0.00 Mombasa 0.00 Murang’a 1.26 Nairobi 1.44 Nakuru 0.93 Nanyuki 0.77 Nyamira 0.00 Nyeri 1.28 Sotik Post 0.26 Name: rain, dtype: float64

Average Temperature by City: city Bungoma 27.16 Eldoret 21.49 Kakamega 25.89 Kericho 23.75 Kiambu 23.15 Kilifi 29.67 Kisii 26.75 Kisumu 26.91 Migori 30.72 Mombasa 28.92 Murang’a 23.51 Nairobi 22.76 Nakuru 23.77 Nanyuki 22.75 Nyamira 27.57 Nyeri 22.11 Sotik Post 24.40 Name: temperature, dtype: float64

temperature humidity rain wind_speed count 17.000000 17.000000 17.000000 17.000000 mean 25.369412 54.764706 0.562353 2.657647 std 2.808217 9.953199 0.611908 1.466542 min 21.490000 34.000000 0.000000 0.430000 25% 23.150000 50.000000 0.000000 1.710000 50% 24.400000 57.000000 0.300000 2.360000 75% 27.160000 60.000000 1.260000 3.110000 max 30.720000 71.000000 1.580000 5.940000

Index([‘date’, ‘city’, ‘longitude’, ‘latitude’, ‘temperature’, ‘feel_like’, ‘max_temperature’, ‘min_temperature’, ‘humidity’, ‘pressure’, ‘wind_speed’, ‘visibility’, ‘rain’, ‘clouds’, ‘weather_condition’, ‘weather_description’], dtype=’object’)

Average Humidity by City: city Bungoma 50.0 Eldoret 60.0 Kakamega 52.0 Kericho 50.0 Kiambu 62.0 Kilifi 60.0 Kisii 41.0 Kisumu 53.0 Migori 34.0 Mombasa 65.0 Murang’a 71.0 Nairobi 57.0 Nakuru 57.0 Nanyuki 57.0 Nyamira 42.0 Nyeri 70.0 Sotik Post 50.0 Name: humidity, dtype: float64

temperature humidity rain wind_speed temperature 1.000000 -0.516919 -0.818492 0.397842 humidity -0.516919 1.000000 0.624435 0.390542 rain -0.818492 0.624435 1.000000 -0.090381 wind_speed 0.397842 0.390542 -0.090381 1.000000

![image](https://github.com/user-attachments/assets/ab9173bd-dc10-4021-a718-e18770789dfb)

![image](https://github.com/user-attachments/assets/790393ab-b256-47f1-9497-afdb132f5ec2)
![image](https://github.com/user-attachments/assets/51d0a64e-c831-4415-b74b-328e8d2d206b)
INSIGHTS FROM THE ABOVE VISUALIZATION
1. Average Temperature by City:
The visualization shows the average temperature for different cities. Cities like Migori and Mombasa have the highest average temperatures (30.72°C and 28.92°C, respectively), while Eldoret has the lowest (21.49°C). This suggests that cities near the coast, like Mombasa, experience higher temperatures due to their proximity to the Indian Ocean, while highland cities like Eldoret are cooler.

2. Average Rainfall by City:
Cities like Kiambu and Murang’a have higher average rainfall, indicating they might experience frequent rain showers. Meanwhile, cities such as Kakamega, Kisii, and Migori show little or no rainfall in the collected data, suggesting that they were drier during the analyzed period. This information is valuable for weather-sensitive activities like agriculture, especially in identifying regions where irrigation may be needed.

3. Temperature Trends Over Time:
The line plot of temperature trends over time can reveal seasonal patterns. For instance, you might observe peaks during warmer months or troughs during cooler periods. These trends can help forecast future weather conditions and guide decision-making in sectors like tourism or farming.

4. Humidity and Rainfall Relationship:
The correlation matrix shows a positive relationship between humidity and rainfall, meaning that regions with higher humidity tend to experience more rainfall. This aligns with general weather patterns, where moisture in the air leads to precipitation.

5. Wind Speed and Weather Factors:
Wind speed has a weak negative correlation with rainfall but a slight positive correlation with temperature. This indicates that higher winds don’t necessarily coincide with rainfall but may be present in warmer conditions.

6. Correlation Insights:
Temperature and Rainfall: There’s a strong negative correlation (-0.81), suggesting that as temperature increases, rainfall tends to decrease. This is typical of arid and semi-arid regions.
Humidity and Rainfall: A positive correlation (0.62) means areas with higher humidity are more likely to experience rainfall, which is expected in tropical climates.
Conclusion Summary:
Coastal areas like Mombasa are warmer, while highland areas like Eldoret are cooler.
Rainfall is more prevalent in regions like Kiambu and Murang’a, making them wetter than others.
Temperature trends help forecast future weather, which is crucial for agriculture and outdoor planning.
The correlation between humidity and rainfall is strong, confirming the importance of moisture levels in predicting rain.
Temperature inversely affects rainfall, indicating drier conditions during hotter periods.
These insights help in making data-driven decisions in weather-sensitive sectors, such as agriculture and tourism, and in better understanding Kenya’s diverse climatic conditions.



