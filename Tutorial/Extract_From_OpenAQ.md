# In this tutorial we want to learn how we can extract online air quality data from OpenAQ by using Python
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
### In the time of creating this Git repository I just installed the following versions of required python libraries: 
pandas: 0.24.2 </br>
requests: 2.22.0 </br>
py-openaq: 1.1.0 </br>
## First code in Python: 
```Python
#importing libraries
import openaq

#Make a request to OpenAQ by using the method so called OpenAQ()
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
### Damping JSON responses into a DataFrame:
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
* *measurements*
It will nicely map a JSON object into the required datetime format which can be easily plotted. The sample python code is as follow: 
```Python
df = api.cities(df=True)

print(df.head(20))
```
