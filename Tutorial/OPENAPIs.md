## Different Open APIs for Air Pollution: 
1. World Air Quality Index [Project](https://aqicn.org/api/) 
2. [Ambee](http://docs.ambeedata.com/)
3. [OpenAQ](https://openaq.org/#/?_k=ogddyg)
4. AirVisual Project and their Air Quality [API](https://www.airvisual.com/)
5. Bitspi AirPollution [API](https://airpollutionapi.com/) (Just provides information in India)

### World Air Quality Index Project
Brief review of JSON [API](https://aqicn.org/json-api/doc/):
* *Access to more than 9000 station level and 600 city level data*
* *Geo-location query (based on latitude/longitude or IP address)*
* *Individual AQI for all pollutants (PM2.5, PM10, NO2, CO, SO2, Ozone)*
* *Station name and coordinates*
* *Originating EPA name and link*
* *Current weather conditions*
* *Stations within a map lat/lng bounds*
* *Search stations by name* </br>
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
### OpenAQ
They aggregate physical air quality data from public data sources provided by government, research-grade and other sources. </br>
##### Note: 
1. They do not validate or transform the data from their originating sources. 
2. Real-time data, by their nature, often have not undergone quality assurance or control processes by their originating sources. 

