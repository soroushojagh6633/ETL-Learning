```JSON
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
