# In this tutorial we will show how the realtime PM2.5 values extracted from OpenAQ can be load into OGC SensorThings API. 
## Table of Contents
1. [OGC SensorThings API Data Model](#ogc-sensorthings-api-data-model)
2. [Mapping between OGC SensorThings API and OpenAQ API](#mapping-between-ogc-sensorthings-api-and-openaq-api)
3. [Creating Things, Location, Datastream, ObservedProperty, and Sensor entities](#creating-things,-location,-datastream,-observedProperty,-and-sensor-entities)

## OGC SensorThings API Data Model:
In this section we want to briefly review the data model of OGC SensorThings API. For detailed information you can refer to OGC SensorThings API [Documentation](https://developers.sensorup.com/docs/#introduction). Totally we have 8 entities in STA data model including: Thing, Sensor, Datastream, ObservedProperty, FeatureOfInterest, Observation, Location, and HistoricalLocation. For creating a Thing in STA, we have to post such a HTML POST query to the server: 
```HTML
http:// http://scratchpad.sensorup.com/OGCSensorThings/v1.0/Things 
```
With The JSON body like: 
```HTML
{
  "name": "Temperature Monitoring System",
  "description": "Sensor system monitoring area temperature",
  "properties": {
    "Deployment Condition": "Deployed in a third floor balcony",
    "Case Used": "Radiation shield"
  },
  "Locations": [{
    "name": "UofC CCIT",
    "description": "University of Calgary, CCIT building",
    "encodingType": "application/vnd.geo+json",
    "location": {
      "type": "Point",
      "coordinates": [-114.133, 51.08]
    }
  }],
  "Datastreams": [{
    "name": "Air Temperature DS",
    "description": "Datastream for recording temperature",
    "observationType": "http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Measurement",
    "unitOfMeasurement": {
      "name": "Degree Celsius",
      "symbol": "degC",
      "definition": "http://www.qudt.org/qudt/owl/1.0.0/unit/Instances.html#DegreeCelsius"
    },
    "ObservedProperty": {
      "name": "Area Temperature",
      "description": "The degree or intensity of heat present in the area",
      "definition": "http://www.qudt.org/qudt/owl/1.0.0/quantity/Instances.html#AreaTemperature"
    },
    "Sensor": {
      "name": "DHT22",
      "description": "DHT22 temperature sensor",
      "encodingType": "application/pdf",
      "metadata": "https://cdn-shop.adafruit.com/datasheets/DHT22.pdf"
    }
  }]
}
```
And a header as follow: 
```Python
header = {'Content-Type': "application/json"}
```
Loading observation is also require sending a request to the ST server wrapped with a JSON object and the same header as follow: 
```HTML
http:// http://scratchpad.sensorup.com/OGCSensorThings/v1.0/Observations 
```
JSON body is as follow:
```HTML
{
  "phenomenonTime": "2017-02-07T18:02:00.000Z",
  "resultTime" : "2017-02-07T18:02:05.000Z",
  "result" : 21.6,
  "Datastream":{"@iot.id":8}
}

## Mapping between OGC SensorThings API and OpenAQ API
So we are in need of extracting a list of metadata from OpenAQ including and the mapping would be as follow: 
### To create a Thing:
* (Mandatory) name: String // "Calgary Central2", OpenAQ API: api.locations(city='ALBERTA', df=True).iloc[3] 
* (Mandatory) description: String // "Air quality monitoring station"  
* (Optional) Deployment Condition: String // "Cathedral Energy DT, On the roof" 
* (Optional) Case Used: String // "To monitor No2, O3, PM2.5, and CO."</br> 
### To create a Locations:
* (Mandatory) name: String // "Cathedral Energy DT"
* (Mandatory) description: String // "623 6 Avenue Southwest, Calgary, Division 6, AB T2P0T5, Downtown Calgary, Calgary Calgary Alberta Canada" 
* (Mandatory) encodingType: ValueCode // "application/vnd.geo+json"
* (Mandatory) location: Any (Depends on encodingType) // "coordinates": [-114.075,51.0476],"type": "Point"</br> OpenAQ API: api.locations(city='ALBERTA', df=True).iloc[3] 
### To create Datastreams:  
* (Mandatory) name:	String // "PM2.5"
* (Mandatory) description	:	String // "Roof pm2.5"
* (Mandatory) unitOfMeasurement:	JSON Object // 
```HTML
"symbol": "ug/m3",
"name": "microgram per cubic meter",
"definition": "https://www.airnow.gov/index.cfm?action=aqibasics.particle"
```
* (Mandatory) observationType:	ValueCode // "http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Measurement"
* (Optional) observedArea: GeoJSON Polygon Object // ""
* (Optional) phenomenonTime: Time Interval (ISO 8601) // ""
* (Optional) resultTime: Time Interval (ISO 8601)// ""</br>
### To create ObservedProperty: 
* (Mandatory) name: String // "pm2.5"
* (Mandatory) definition:	URI // "https://en.wikipedia.org/wiki/Particulates"
* (Mandatory) description: String // "PM2.5 Particulates"</br> 
### To create Sensor: 
* (Mandatory) name:	String // "Central2_PM2.5 sensor" 
* (Mandatory) description:	String // "This sensor is deployed by Environment Canada - Air Quality Services"
* (Mandatory) encodingType:	ValueCode // "application/pdf" 
* (Mandatory) metadata: Any (depending on the value of the encodingType) // "https://en.wikipedia.org/wiki/Particulates"</br>
### To create Observations: 
* (Mandatory) phenomenonTime:	Time(Interval) String (ISO 8601) // OpenAQ API: "api.latest(city='ALBERTA', location='Calgary Central2', parameter='pm25', df=True)" 
* (Mandatory) result: Any (depends on the observationType defined in the associated Datastream) // OpenAQ API: "api.latest(city='ALBERTA', location='Calgary Central2', parameter='pm25', df=True)" 
* (Mandatory) resultTime:	Time(Interval) String (ISO 8601) // OpenAQ API: "api.latest(city='ALBERTA', location='Calgary Central2', parameter='pm25', df=True)"
* Datastream id: extracted from the response of first query of STA
* (Optional) resultQuality: DQ_Element // ""
* (Optional) validTime:	Time Interval String (ISO 8601) // ""
* (Optional) parameters: JSON Object // "" </br>?


