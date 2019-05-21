## Different Open APIs for Air Pollution: 
1. World Air Quality Index [Project](https://aqicn.org/api/) 
2. AirNow [API](https://docs.airnowapi.org/)
3. [Ambee](http://docs.ambeedata.com/)
4. [OpenAQ](https://openaq.org/#/?_k=ogddyg)
5. AirVisual Project and their Air Quality [API](https://www.airvisual.com/)
6. Bitspi AirPollution [API](https://airpollutionapi.com/) (Just provides information in India)

### 1- World Air Quality Index [Project](http://aqicn.org/contact/)
Brief review of JSON [API](https://aqicn.org/json-api/doc/):
* *Access to more than 9000 stations worldwide ~ that’s around 600 major cities in 70 [countries](http://aqicn.org/sources/)* 
* *Fully cover Canada and the US*
* *Geo-location query (based on latitude/longitude or IP address)*
* *Individual AQI for all pollutants (PM2.5, PM10, NO2, CO, SO2, Ozone)*
* *Station name and coordinates*
* *Originating EPA name and link*
* *Current weather conditions*
* *Stations within a map lat/lng bounds*
* *Search stations by name* </br>
### Limitations: 
1. It does not support historical data
2. Maximum of 10 requests per second per user (all the users with the same token consider as a user) 
### Strengths
-API documentation Rating="****"
-Online and interactive query maker
##### Sample JSON data:
```HTTP Request:
url: https://api.waqi.info/feed/Calgary/?token=12344444...
JSON Response:
{
    "status": "ok",
    "data": {
        "aqi": 12,
        "idx": 5477,
        "attributions": [
            {
                "url": "http://aep.alberta.ca/air/",
                "name": "Alberta Environment and Sustainable Resource Development - Air Quality monitoring"
            },
            {
                "url": "https://waqi.info/",
                "name": "World Air Quality Index Project"
            }
        ],
        "city": {
            "geo": [
                51.0471506,
                -114.0731477
            ],
            "name": "Calgary Central 2, Alberta, Canada",
            "url": "https://aqicn.org/city/canada/alberta/calgary-central-2"
        },
        "dominentpol": "pm25",
        "iaqi": {
            "co": {
                "v": 1.3
            },
            "h": {
                "v": 42.7
            },
            "no2": {
                "v": 2.8
            },
            "o3": {
                "v": 0.1
            },
            "p": {
                "v": 1005.9
            },
            "pm25": {
                "v": 12
            },
            "so2": {
                "v": 1.5
            },
            "t": {
                "v": 7.7
            },
            "w": {
                "v": 1.5
            }
        },
        "time": {
            "s": "2019-05-20 19:00:00",
            "tz": "-06:00",
            "v": 1558378800
        },
        "debug": {
            "sync": "2019-05-21T10:24:49+09:00"
        }
    }
}
```
#### Steps to extract data from JSON API: 
1. Get token for all API access from [here](https://aqicn.org/data-platform/token/#/)
2. Request the api based on their proposed [doc](https://aqicn.org/json-api/doc/) </br>
### 2- AirNow [API](https://docs.airnowapi.org/)
#### A brief review of AirNow:
The U.S. EPA [AirNow program](www.AirNow.gov) protects public health by providing forecast and real-time observed air quality information across the United States, Canada, and Mexico. AirNow receives real-time air quality observations from over 2,000 monitoring stations and collects forecasts for more than 300 cities. 
##### Sample url and JSON response (Lon Angeles)
```HTTP Request
url = http://www.airnowapi.org/aq/observation/latLong/current/?format=application/json&latitude=34.0410&longitude=-118.3444&distance=25&API_KEY=1111
```
```JSON Response
[
  {
    "DateObserved": "2019-05-20 ",
    "HourObserved": 18,
    "LocalTimeZone": "PST",
    "ReportingArea": "NW Coastal LA",
    "StateCode": "CA",
    "Latitude": 34.0505,
    "Longitude": -118.4566,
    "ParameterName": "O3",
    "AQI": 47,
    "Category": {
      "Number": 1,
      "Name": "Good"
    }
  },
  {
    "DateObserved": "2019-05-20 ",
    "HourObserved": 18,
    "LocalTimeZone": "PST",
    "ReportingArea": "NW Coastal LA",
    "StateCode": "CA",
    "Latitude": 34.0505,
    "Longitude": -118.4566,
    "ParameterName": "PM2.5",
    "AQI": 11,
    "Category": {
      "Number": 1,
      "Name": "Good"
    }
  }
]
```

### 3- OpenAQ 
OpenAQ is the world's first open, real-time and historical air quality platform, aggregating government-measured and research-grade data - entirely open-source. They aggregate physical air quality data from public data sources provided by government, research-grade and other sources. </br>
#### Stored data in Open AQ platform: 
PM2.5, PM10, CO, SO2, NO2, O3, and BC. You can read more [here](https://github.com/openaq/openaq-info/blob/master/FAQ.md) and [here](https://medium.com/@openaq/how-in-the-world-do-you-access-air-quality-data-older-than-90-days-on-the-openaq-platform-8562df519ecd)
##### Note: 
1. They do not validate or transform the data from their originating sources. 
2. Real-time data, by their nature, often have not undergone quality assurance or control processes by their originating sources.
##### Limitation:
There are IP-based limits imposed of ~2000 requests over a 5 minute period. If you are running into a number of failed requests, please check the rate of requests.</br>
##### Open AQ Platform [API](https://docs.openaq.org/)
[Sample](https://dolugen.github.io/openaq-browser/#/) http request and response: 
```HTTP Request
url = https://api.openaq.org/v1/measurements?country=CA&city=ALBERTA&location=Calgary+Central2&parameter=pm25&date_from=2019-05-20&date_to=2019-05-20
```
```JSON Response
{
  "meta": {
    "name": "openaq-api",
    "license": "CC BY 4.0",
    "website": "https://docs.openaq.org/",
    "page": 1,
    "limit": 100,
    "found": 1
  },
  "results": [
    {
      "location": "Calgary Central2",
      "parameter": "pm25",
      "date": {
        "utc": "2019-05-20T00:00:00.000Z",
        "local": "2019-05-19T17:00:00-07:00"
      },
      "value": 4.5,
      "unit": "µg/m³",
      "coordinates": {
        "latitude": 51.047611,
        "longitude": -114.075165
      },
      "country": "CA",
      "city": "ALBERTA"
    }
  ]
}
```
