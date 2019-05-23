# In this tutorial we want to learn how we can extract real-time air quality data from OpenAQ by using Python
## Table of Content
1. [Requirements](#requirements)
2. [Setting up the environment](#how-to-install-libraries)
3. [OpenAQ Data Model](#openaq-api-data-model)
4. [Version of python libraries](#version-of-python-libraries)
5. [Hello World](#first-code-in-python)
6. [Damping JSON Objects into a DataFrames](#damping-json-responses-into-a-dataframe)
7. [Data Visualization & statistics](#data-visualization)
8. [Extracting real-time PM2.5 values](#extracting-the-latest-pm2.5)
9. [Summary](#summary)
## Requirements: 
1. Python 2.7+
2. Pandas
3. requests
4. py-openaq
## How to install libraries: 
You can install the package directly via pypi through pip:
```
>>> pip install py-openaq
```
## OpenAQ API data model: 
The API is working with 8 different entities including:
* cities: provides a simple listing of cities within the platforms
* countries: provides a simple listing of countries within the platform
* fetches: providing data about individual fetch operations that are used to populate data in the platform
* latest: provides the latest value of each available parameter for every location in the system
* locations: provides a list of measurement locations and their meta data
* measurements: provides data about individual measurements
* parameters: provides a simple listing of parameters within the platform
* sources: provides a list of data sources

## Version of python libraries
In the time of creating this Git repository I just installed the following versions of required python libraries: 
pandas: 0.24.2 </br>
requests: 2.22.0 </br>
py-openaq: 1.1.0 </br>
## First code in Python: 
```Python
#importing libraries
import openaq

#Make an instance of the openaq.OpenAQ class to extract data
api = openaq.OpenAQ()
# Status will be (200:"Success", 40x: "Bad Request, and 500: "Server Error")
# resp is the JSON object that comes from the server 
status, resp = api.cities()
print("Statu: " + str(status)  + "\n" + "JSON: " + str(resp))
# print("Status: " + str(status) + "\n" + "JSON: " + resp)
```
### The result will be something like this which shows the first 100 cities extracted from OpenAQ
```HTML
{
  'meta': {
    'name': 'openaq-api',
    'license': 'CC BY 4.0', 
    'website': 'https://docs.openaq.org/',
    'page': 1,
    'limit': 100,
    'found': 2658,
    'pages': 27
  },
  'results': [
    {
      'city': 'Escaldes-Engordany',
      'country': 'AD',
      'locations': 2,
      'count': 66082
    },
    {
      'city': 'unused',
      'country': 'AD',
      'locations': 1,
      'count': 3707
    },
    {
      'city': 'Abu Dhabi',
      'country': 'AE',
      'locations': 1,
      'count': 19649
    },
    {
      'city': 'Dubai',
      'country': 'AE',
      'locations': 1,
      'count': 12645
    },
    {
      'city': 'Buenos Aires',
      'country': 'AR',
      'locations': 4,
      'count': 14976
    },...
 ]
}
```
## Damping JSON responses into a DataFrame:
We want to work with JSON objects and Pandas provides a great chance to easily damp JSON object into a DataFrame. 
```Python
from pandas.io.json import json_normalize

df = json_normalize(resp)
print(df.head(20))
```
As of v 0.3.0 of py-openaq, DataFrame is added to the following methods: 
* *cities*
* *countries*
* *latest*
* *locations*
* *measurements* </br>

It will nicely map a JSON object into the required datetime format which can be easily plotted. The sample python code is as follow: 
```Python
df = api.cities(df=True)

print(df.head(20))
```

How to extract limited number of results and damp the JSON object in a DataFrame:
```Python
#Extracting all the cities from OpenAQ
resp = api.cities(df=True, limit=10000)
print (resp.head(20))

# display the first 10 rows
resp.info()
```
How to make a query of extracted results:
```Python
#Making a query which show information about the Canada
print (resp.query("country == 'CA'"))
```
Result:</br>

![alt text](https://github.com/soroushojagh6633/ETL-Learning/blob/master/img/CA_cities_OpenAQ.PNG "Information of OpenAQ in Canada")

Getting all the parameters available in OpenAQ:
```Python
#Extracting all the parameters available in OpenAQ
res = api.parameters(df=True)

print (res)
```
Result:</br>

![alt text](https://github.com/soroushojagh6633/ETL-Learning/blob/master/img/Parameters_OpenAQ.PNG "Parameters available in OpenAQ")

Getting general information about the city of Calgary, AB, Canada:

```Python
#Getting all the information related to the city of Calgary
res = api.locations(city='ALBERTA', df=True)

print(res.iloc[3])
print(res.iloc[4])
```
Result:</br>

![alt text](https://github.com/soroushojagh6633/ETL-Learning/blob/master/img/Info_Calgary_OpenAQ.PNG "General Information about the city of Calgary_Central station")
![alt text](https://github.com/soroushojagh6633/ETL-Learning/blob/master/img/Info_Calgary_NW_OpenAQ.PNG "General Information about the city of Calgary_NW station")

How to grab the latest value of PM2.5 in Calgary Central station?

```Python
# Getting the latest value of PM2.5 for the central station located in Calgary
res = api.latest(city='ALBERTA', location='Calgary Central2', parameter='pm25')

print(res)
```
Results in JSON format is as follows:
```HTML
(200,
{
  'meta': {
    'name': 'openaq-api',
    'license': 'CC BY 4.0',
    'website': 'https://docs.openaq.org/',
    'page': 1,
    'limit': 100,
    'found': 1,
    'pages': 1
  },
  'results': [
    {
      'location': 'Calgary Central2',
      'city': 'ALBERTA',
      'country': 'CA',
      'distance': 7164117.352361423,
      'measurements': [
        {
          'parameter': 'pm25',
          'value': 5,
          'lastUpdated': '2019-05-22T19:00:00.000Z',
          'unit': 'µg/m³',
          'sourceName': 'AirNow',
          'averagingPeriod': {
            'value': 1,
            'unit': 'hours'
          }
        }
      ],
      'coordinates': {
        'latitude': 51.04761,
        'longitude': -114.075165
      }
    }
  ]
})
```
It can be simply damp into a DataFrame by using: 
```Python
# Getting the latest value of PM2.5 for the central station located in Calgary
res = api.latest(city='ALBERTA', location='Calgary Central2', parameter='pm25', df=True)

print(res.head(10))
```
Getting 10000 last PM2.5 measurements for the city of Calgary: 
```Python
#Getting the last 10000 PM2.5 measurements in the city of Calgary, AB
res = api.measurements(city='ALBERTA', location='Calgary Central2', parameter='pm25', limit=10000, df=True)

print(res.head(100))
```
## Data Visualization 
After damping the data into a DataFrame we can do some data visualization and data summarization to get a general view or maybe do some comparision between different locations. Here in this tutorial I want to show, summarize, and compare the value of PM2.5 between all the stations in the Alberta, Canada. 
#### * First step: Extracting 10,000 measurements of PM2.5 from the desired stations and calculate statistics about the extracted data

```Python
#Extract 10,000 PM2.5 measurements for all the stations in Alberta  
res = api.measurements(city='ALBERTA', parameter='pm25', limit=10000, df=True)

pandas.set_option('display.max_columns', 9)
# Print out the statistics on a per-location basiss
print(res.groupby(['location'])['value'].describe().head(10))
```
Result which shows Count, Min, Max, Mean, Standard Deviation, Q1, Median, and Q3:</br>

![alt text](https://github.com/soroushojagh6633/ETL-Learning/blob/master/img/Alberta_Statistics_OpenAQ.PNG "Statistics about the 10,000 latest measurements extracted from all the stations in Alberta, Canada")
#### * Second step: Visualizing the extracted data for each station as a Time Series
```Python 
fig, ax = plt.subplots(1, figsize=(10, 6))

for group, df in res.groupby('location'):
    # Query the data to only get positive values and resample to hourly
    _df = df.query("value >= 0.0").resample('1h').mean()

    _df.value.plot(ax=ax, label=group)

ax.legend(loc='best')
ax.set_ylabel("$PM_{2.5}$  [$\mu g m^{-3}$]", fontsize=20)
ax.set_xlabel("")
sns.despine(offset=5)

plt.legend(bbox_to_anchor=(1.05, 1), loc=2, borderaxespad=0.)

plt.show()
```
Result: All the time series shown as follow:</br>
![alt text](https://github.com/soroushojagh6633/ETL-Learning/blob/master/img/Statistics_Plot.png "Time series plotted from all the 10,000 PM2.5 measurements from all the stations in Alberta, Canada")

The Box-Plot of the extracted data shown as follow:
```Python
fig, ax = plt.subplots(1, figsize=(14,7))

ax = sns.boxplot(
    x='location',
    y='value',
    data=res.query("value >= 0.0"),
    fliersize=0,
    palette='deep',
    ax=ax)

ax.set_ylim([0, 25])
ax.set_ylabel("$PM_{2.5}\;[\mu gm^{-3}]$", fontsize=18)
ax.set_xlabel("")

sns.despine(offset=10)

plt.xticks(rotation=90)
plt.show()
```
Result: Box-Plot of extracted data:</br>
![alt text](https://github.com/soroushojagh6633/ETL-Learning/blob/master/img/Box_plot.png "Box-Plot from all the 10,000 PM2.5 measurements from all the stations in Alberta, Canada")

### Extracting the latest PM2.5 values every five minutes
We are aware that the frequency of updating air quality parameters in OpenAQ is one hour. But here to meet the requirements of our assignment, we want to request pm2.5 values every five minutes. 

To eschedule events which is responsible to request real-time PM2.5 values, I used [sched](https://docs.python.org/3/library/sched.html) python module as follow:

```Python
import sched, time

#Instantiate sched module
s = sched.scheduler(time.time, time.sleep)

#The desired request function to get PM2.5 values
def request_OpenAQ(sc):
    # Getting the latest value of PM2.5 for the central station located in Calgary
    res = api.latest(city='ALBERTA', location='Calgary Central2', parameter='pm25', df=True)

    print(res)
    s.enter(300, 1, request_OpenAQ, (sc,))

s.enter(0, 1, request_OpenAQ, (s,))
s.run()
```
Result: </br>
![alt text](https://github.com/soroushojagh6633/ETL-Learning/blob/master/img/Real-time_OpenAQ.PNG "Getting the last PM2.5 every five minutes")

### Summary
In all, through this tutorial we could hopefuly extract real-time PM2.5 values from all the desired stations located in Alberta, Canada. In addition, visualization of data plays an important role in getting a general knowledge about the extracted data. We could hopefuly represent extracted data as time series for all the stations located in Alberta. In the next tutorial, we will load the extracted data into an instances of OGC SensorThings API server.   
