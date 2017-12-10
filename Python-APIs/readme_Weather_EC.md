```python
# Dependicies
import requests as req
import matplotlib.pyplot as plt
import pandas as pd
import json
import numpy as np

import random as random
import csv
import seaborn as sns
#import openweathermapy as ow
```

```python
# Save config info

endpoint = 'http://api.openweathermap.org/data/2.5/weather'
api_key = 'cfcd684b00f542abb1f1b3ef1ec41917'
units='metric'

```

```python
# Build a partial query url

# q is the location i'm asking to get, in this case cities

params = {
    'appid': api_key,
    'units': 'imperial',
}
```

```python
#Read in countries to cities into a json

all_cities={}

with open('countriesToCities.json', 'r', encoding='utf-8') as myfile:
    all_cities = json.loads(myfile.read())
```

```python
# Flatten cities list

cities_list = list(all_cities.values())
flat_cities_list = [item for sublist in cities_list for item in sublist]

```

```python
#Choose 500 Random cities

choosen_cities = random.sample(flat_cities_list, 800)
choosen_cities
```

```python
weather_data = []
row_count = 0

# Loop through each city in city list, and request for data on each

for city in choosen_cities:
    params['q'] = city
    weather_response = req.get(endpoint, params = params)
    
    target_url = weather_response.url
    
    print('Now retrieving city #' + str(row_count) + ' ' + city)
    print(target_url)
    row_count += 1
    

    weather_json = weather_response.json()
    weather_data.append(weather_json)
    
#weather_data
```

```python
weather_df = pd.DataFrame(choosen_cities)
weather_df.columns = ['City']

weather_df['Latitude'] = ''
weather_df['Longitude'] = ''
weather_df['Temperature'] = ''
weather_df['Clouds'] = ''
weather_df['Wind'] = ''
weather_df['Humidity'] = ''

weather_df.head()
```

```python

lat_data = []
for index, city_record in weather_df.iterrows():
    try:
        weather_df.set_value(index, "Latitude", weather_data[index].get("coord").get('lat'))
    except AttributeError:
        weather_df.set_value(index, "Latitude", None)
        
    try:
        weather_df.set_value(index, 'Longitude', weather_data[index].get('coord').get('lon'))
    except AttributeError:
        weather_df.set_value(index, 'Longitude', None)
        
    try:
        weather_df.set_value(index, 'Temperature', weather_data[index].get('main').get('temp'))
    except AttributeError:
        weather_df.set_value(index, 'Temperature', None)
        
    try:
        weather_df.set_value(index, 'Humidity', weather_data[index].get('main').get('humidity'))
    except AttributeError:
        weather_df.set_value(index, 'Humidity', None)
        
    try:
        weather_df.set_value(index, 'Clouds', weather_data[index].get('clouds').get('all'))
    except AttributeError:
        weather_df.set_value(index, 'Clouds', None)
        
    try:
        weather_df.set_value(index, 'Wind', weather_data[index].get('wind').get('speed'))
    except:
        weather_df.set_value(index, 'Wind', None)
        
#def get_longitude(record):
#    try:
#        return record['coord']['lon']
#    except KeyError:
#        return None
    


weather_df.head()
```

```python
weather_df.replace('None', np.nan, inplace=True)

weather_df.dropna(axis=0, how='any', inplace=True)

weather_df.count()
```

```python
# Temperature (F) vs. Latitude
# Build a scatter plot for each data type

sns.set()

plt.scatter(weather_df["Latitude"],
           weather_df["Temperature"],
           edgecolor='black', linewidths=1, marker='o',
           alpha=0.8, c='b',label='City')

# Other graph properties

plt.title('Temperature (F) vs Latitude (Analysis Date: 12/10/17)')
plt.xlabel('Latitude')
plt.ylabel('Temperature')
plt.grid(True)
plt.xlim([-60, 80])
plt.ylim([-20, 120])

# Save figure
plt.savefig('Temperature_vs_Latitude.png')

# Plot
plt.show()
```

```python
# Temperature (F) vs. Latitude
# Build a scatter plot for each data type

sns.set()

plt.scatter(weather_df["Latitude"],
           weather_df["Humidity"],
           edgecolor='black', linewidths=1, marker='o',
           alpha=0.8, c='b',label='City')

# Other graph properties

plt.title('Humidity % vs Latitude  (Analysis Date: 12/10/17)')
plt.xlabel('Latitude')
plt.ylabel('Humidity (%)')
plt.grid(True)
plt.xlim([-60, 80])
plt.ylim([-20, 120])

# Save figure
plt.savefig('Humidity_vs_Latitude.png')

# Plot
plt.show()

```

```python
# Cloudiness vs. Latitude
# Build a scatter plot for each data type

sns.set()

plt.scatter(weather_df["Latitude"],
           weather_df["Clouds"],
           edgecolor='black', linewidths=1, marker='o',
           alpha=0.8, c='b',label='City')

# Other graph properties

plt.title('Cloudiness % vs Latitude (Analysis Date: 12/10/17)')
plt.xlabel('Latitude')
plt.ylabel('Cloudiness (%)')
plt.grid(True)
plt.xlim([-60, 80])
plt.ylim([-20, 120])

# Save figure
plt.savefig('Cloudiness_vs_Latitude.png')

# Plot
plt.show()
```

```python
# Wind Speed (mph) vs. Latitude
# Build a scatter plot for each data type

sns.set()

plt.scatter(weather_df["Latitude"],
           weather_df["Wind"],
           edgecolor='black', linewidths=1, marker='o',
           alpha=0.8, c='b',label='City')

# Other graph properties

plt.title('Wind (mph) % vs Latitude (Analysis Date: 12/10/17)')
plt.xlabel('Latitude')
plt.ylabel('Wind (mph)')
plt.grid(True)
plt.xlim([-60, 80])
plt.ylim([-5, 40])

# Save figure
plt.savefig('Wind_vs_Latitude.png')

# Plot
plt.show()
```

Data Analysis - (12-10-17)

1. Most of the city data received was taken in the
northern hemisphere.  

2. There are almost no cities above 60 degrees latitude
and below -40 degrees latitude.  

3. Most cities that have a significant
population are in cooler areas of the world.  These cities have a temperature
between 25 and 60 degrees Fahrenheit. 

4. Higher winds occur between 40 and 60
degrees latitude in the northern and southern hemisphere. 

5. Humidity and
cloudiness seem to correlatewith each other.

```python

```
