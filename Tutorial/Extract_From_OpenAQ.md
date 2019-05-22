# In this tutorial we want to learn how we can extract online air quality data from OpenAQ by using Python
## Requirements: 
1. Python 2.7+
2. Pandas
3. seaborn
4. requests
5. py-openaq
## How to install libraries: 
You can install the package directly via pypi through pip:

cmd: pip install py-openaq

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
```JSON
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
