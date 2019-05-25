```Python
# Importing required python libraries
import openaq
import requests
import json
import sched, time,datetime


# Defining required parameters based on Mapping between OpenAQ and STA
# Request related variables
STA_ScratchPad_URL = "http://scratchpad.sensorup.com/OGCSensorThings/v1.0/"
header = {'Content-Type': "application/json"}

# Things related variables
thingsName = ""
thingsDescription = "Air quality monitoring station"
thingsDeploymentCondition = "Cathedral Energy DT, On the roof"
thingsCasedUse = "To monitor No2, O3, PM2.5, and CO"

# Location related variables
locationName = "Cathedral Energy DT"
locationDescription = "623 6 Avenue Southwest, Calgary, Division 6, AB T2P0T5, Downtown Calgary, Calgary Calgary Alberta Canada"
locationEncodingType = "application/vnd.geo+json"
locationCoordinates = []

# Datastream related variables
datastreamName = ""
datastreamId = -9999
datastreamDescription = "Roof pm2.5"
datastreamUnitOfMeasurementSymbol = ""
datastreamUnitOfMeasurementName = "microgram per cubic meter"
datastreamUnitOfMeasurementDefinition = "https://www.airnow.gov/index.cfm?action=aqibasics.particle"
datastreamObservationType = "http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Measurement"

# ObservedPrperty related variables
observedPropertyName = ""
observedPropertyDescription = "PM2.5 Particulates"
observedPropertyDefinition = "https://en.wikipedia.org/wiki/Particulates"

# Sensor related variables
sensorName = "Central2_PM2.5 sensor"
sensorDescription = "This sensor is deployed by Environment Canada - Air Quality Services"
sensorEncodingType = "application/pdf"
sensorMetadata = "https://en.wikipedia.org/wiki/Particulates"

# Observations related variables
observationPhenomenonTime = ""
observationResultTime = ""
observationResult = -9999


# This function is responsible to request PM2.5 observations every five minutes
def request_OpenAQ(sc):
    # Getting the latest value of PM2.5 for the central station located in Calgary
    try:
        status, response = api.latest(city='ALBERTA', location='Calgary Central2', parameter='pm25')
        print("Successfully connected with OpenAQ. Status code: "+str(status))

        # Loading process of of the returned response into a python JSON object
        openaqResponse = json.dumps(response)
        openaqResponseJSON = json.loads(openaqResponse)

        # Searching through the JSON object and updating the required values
        return_values_JSON(openaqResponseJSON)

        # Checking about existing the desired Thing in ScratchPad
        if (checkThingsExisting(thingsName)):
            # If the desired Thing exists, just load the observation by 1. making the JSON object for the request body by makingSTA_JSON_Body method
            # 2. post the request

            ObservationJSON = json.dumps(makingSTA_JSON_Body("LoadingObservation", "observation.json"))
            postRequestToSTA("LoadingObservation", ObservationJSON)

        else:
            # If not, first create the new Thing by 1. making the JSON object for the request body by makingSTA_JSON_Body method
            # 2. post the request
            ThingsJSON = makingSTA_JSON_Body("CreatingThings", "things.json")
            postRequestToSTA("CreatingThings", ThingsJSON)

            # Then load observations to it
            ObservationJSON = json.dumps(makingSTA_JSON_Body("LoadingObservation", "observation.json"))
            postRequestToSTA("LoadingObservation", ObservationJSON)

        # Calling this method after 300 seconds (every 5 minutes)
        customChed.enter(300, 1, request_OpenAQ, (sc,))

    except:
        print("Failed to connect OpenAQ API")


# Method to check if the desired Thing exists in ScratchPad or not
def checkThingsExisting(desiredName):
    global header,STA_ScratchPad_URL

    # Preparing the suitable URL
    query = "Things?$filter=substringof('" + desiredName + "',name)"
    checkingURL = STA_ScratchPad_URL + query

    try:

        # Sending the request by using the prepared parameters
        response = requests.request("GET", checkingURL, headers=header)
        print("Successfully connected to OGC SensorThings API. Status code:"+str(response.status_code))

        # Creating python JSON object from the returned response
        responseJSON = response.json()

        # Looking into the number of Things with the exact name,
        if (responseJSON["@iot.count"] > 0):
            return True  # Things exist
        else:
            return False  # Things does not exist

    except requests.exceptions.RequestException as error:
        print ("Failed to connect OGC SensorThings API because of: "+error)

# Updating required values from the JSON object returned back from OpenAQ API
def return_values_JSON(jsonObject):
    global thingsName, locationCoordinates, datastreamName,datastreamUnitOfMeasurementSymbol, observedPropertyName, observationPhenomenonTime, observationResultTime, observationResult

    # Updating global variables required for mapping between OpenAQ and STA by using returned JSON
    thingsName = jsonObject['results'][0]['location']
    locationCoordinates = [jsonObject['results'][0]['coordinates']['longitude'],jsonObject['results'][0]['coordinates']['latitude']]
    datastreamName = jsonObject['results'][0]['measurements'][0]['parameter']
    datastreamUnitOfMeasurementSymbol = jsonObject['results'][0]['measurements'][0]['unit']
    observedPropertyName = jsonObject['results'][0]['measurements'][0]['parameter']
    observationPhenomenonTime = jsonObject['results'][0]['measurements'][0]['lastUpdated']
    observationResultTime = datetime.datetime.utcnow().isoformat() #This is the local time in UTC ISO 8610 format
    observationResult = jsonObject['results'][0]['measurements'][0]['value']

# Reading sample json file (for creating Things or loading observations)
def readJSON(fileName):
    with open(fileName) as file:
        jsonFile = json.load(file)
    return jsonFile

# This method is responsible for updating the sample json format for both creating new Things
# or loading new observation based on the data returned back from OpenAQ API. In other words,
# to make this script reusable, a sample json file is stored, then its properties will be changed
# based on the data returned back from OpenAQ API. So, if we change the city, air pollutant, or other
# parameters, this python script still work fine.
def makingSTA_JSON_Body (function,sampleJSON):
    global thingsName,thingsDescription, thingsDeploymentCondition, thingsCasedUse
    global locationName, locationDescription, locationEncodingType, locationCoordinates
    global datastreamName, datastreamId, datastreamDescription, datastreamUnitOfMeasurementSymbol, datastreamUnitOfMeasurementName, datastreamUnitOfMeasurementDefinition, datastreamObservationType
    global observedPropertyName, observedPropertyDescription, observedPropertyDefinition
    global sensorName, sensorDescription, sensorEncodingType, sensorMetadata
    global observationPhenomenonTime, observationResultTime, observationResult

    # updating properties of sample json file whenever we decide to create a new Thing in STA
    if (function == "CreatingThings"):
        ThingsJSON = readJSON(sampleJSON)

        # Updating Thing's related parameters based on values returned back from OpenAQ
        ThingsJSON['name'] = thingsName
        ThingsJSON['description'] = thingsDescription
        ThingsJSON['properties']["Deployment Condition"] = thingsDeploymentCondition
        ThingsJSON['properties']["Case Used"] = thingsCasedUse

        # Updating Location's related parameters based on values returned back from OpenAQ
        ThingsJSON['Locations'][0]["name"] = locationName
        ThingsJSON['Locations'][0]["description"] = locationDescription
        ThingsJSON['Locations'][0]["encodingType"] = locationEncodingType
        ThingsJSON['Locations'][0]["location"]["coordinates"] = locationCoordinates

        # Updating Datastream's related parameters based on values returned back from OpenAQ
        ThingsJSON['Datastreams'][0]["name"] = datastreamName
        ThingsJSON['Datastreams'][0]["description"] = datastreamDescription
        ThingsJSON['Datastreams'][0]["observationType"] = datastreamObservationType
        ThingsJSON['Datastreams'][0]["unitOfMeasurement"]["name"] = datastreamUnitOfMeasurementName
        ThingsJSON['Datastreams'][0]["unitOfMeasurement"]["symbol"] = datastreamUnitOfMeasurementSymbol
        ThingsJSON['Datastreams'][0]["unitOfMeasurement"]["definition"] = datastreamUnitOfMeasurementDefinition

        # Updating ObservedProperty's related parameters based on values returned back from OpenAQ
        ThingsJSON['Datastreams'][0]["ObservedProperty"]["name"] = observedPropertyName
        ThingsJSON['Datastreams'][0]["ObservedProperty"]["description"] = observedPropertyDescription
        ThingsJSON['Datastreams'][0]["ObservedProperty"]["definition"] = observedPropertyDefinition

        # Updating Sensor's related parameters based on values returned back from OpenAQ
        ThingsJSON['Datastreams'][0]["Sensor"]["name"]  = sensorName
        ThingsJSON['Datastreams'][0]["Sensor"]["description"] = sensorDescription
        ThingsJSON['Datastreams'][0]["Sensor"]["encodingType"] = sensorEncodingType
        ThingsJSON['Datastreams'][0]["Sensor"]["metadata"] = sensorMetadata

        print(ThingsJSON)
        return ThingsJSON # Returning the manipulated JSON file

    elif (function == "LoadingObservation"):
        ObservationJSON = readJSON(sampleJSON)

        # Updating Observation's related parameters based on values returned back from OpenAQ
        ObservationJSON["phenomenonTime"] = observationPhenomenonTime
        ObservationJSON["resultTime"] = observationResultTime
        ObservationJSON["result"] = observationResult
        ObservationJSON["Datastream"]["@iot.id"] = getDatastreamIdFromSTA() #request getDatastreamIdFromSTA() to get DatastreamId

        return ObservationJSON # Returning the manipulated JSON file

# This method is responsible for sending proper request to the STA
def postRequestToSTA(function, jsonObject):
    global header,STA_ScratchPad_URL,thingsName

    if (function == "CreatingThings"):

        # Making an appropriate URL to create a new Thing
        creatingThingsURL = STA_ScratchPad_URL + "Things"

        try:

            # Sending request to STA with desired parameters
            STA_response = requests.request("POST", creatingThingsURL, json=jsonObject, headers=header)
            print("Thing is successfully created in STA. Status code:"+str(STA_response.status_code))

        except requests.exceptions.RequestException as error:
            print("Failed to create new Things because of: " + error)

    elif (function == "LoadingObservation"):

        # Making an appropriate URL for loading new Observation
        ObservationsURL = STA_ScratchPad_URL + "Datastreams("+ str(getDatastreamIdFromSTA())+")/Observations"

        try:

            # Sending request to STA with desired parameters
            STA_response = requests.request("POST", ObservationsURL, data=jsonObject, headers=header)
            print("Observation is successfully loaded to STA at UTC datetime: "+datetime.datetime.utcnow().isoformat()+ "." + " Status code:" + str(STA_response.status_code))

        except requests.exceptions.RequestException as error:
            print("Failed to load new observation because of: " + error)


# This method is responsible for returning back Datastream's id which belongs to the specific Things
# The name of this Thing has been chosen based on data returned back from OpenAQ. So, if we decide to
# use another city, the Datastream id associated with the new Thing will be returned automatically
def getDatastreamIdFromSTA():

    # Preparing suitable query
    query = "Things?$filter=substringof('" + thingsName + "',name)&$expand=Datastreams($select=id)&$select=Datastreams"
    loadingObservationsURL = STA_ScratchPad_URL + query

    try:

        # Sending request to STA with desired parameters
        response = requests.request("GET", loadingObservationsURL, headers=header)
        responseJSON = response.json()

        # Finding Datastream id from the returned JSON response
        datastreamID = responseJSON["value"][0]["Datastreams"][0]["@iot.id"]

        return datastreamID

    except requests.exceptions.RequestException as error:
        print("This error has been raised: " + error)

#Instantiate sched module
customChed = sched.scheduler(time.time, time.sleep)

#Instantiate openaq.OpenAQ module
api = openaq.OpenAQ()

# Calling request_OpenAQ method to connect OpenAQ API and send request for latest observations
customChed.enter(0, 1, request_OpenAQ, (customChed,))
customChed.run()

```




