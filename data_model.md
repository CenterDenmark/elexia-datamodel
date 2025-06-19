````mermaid
erDiagram

%% Note that all timestamps should be XSD timestamps. 
%% All relations except those for PropertyValue and PropertyValueInterval are physically represented in the Relations table and not as columns on the different tables 

PropertyValue }|--|| Observation: hasResult

PropertyValueInterval }|--|| Observation: hasResult


%% isMeasuredIn is the inverse
Observation }|--|| UnitOfMeasure: isUnitOf

Observation }|--|| Property: isObservedBy

Observation }|--|| AccumulationKind: isKindOf

Observation }|--|| AggregationKind: isKindOf

Observation }|--|| Device: madeExecution

ForecastObservation }|--|| ForecastModel: madeExecution

ForecastData }|--|| ForecastObservation: hasResult

ForecastModel }|--|| Device: hasForecast

ForecastModel }|--|| Building: hasForecast

ForecastModel }|--|| BuildingSpace: hasForecast

ForecastModel }|--|| Site: hasForecast

ForecastObservation }|--|| UnitOfMeasure: isUnitOf

ForecastObservation }|--|| Property: isForecastedBy

ForecastObservation }|--|| AccumulationKind: isKindOf

ForecastObservation }|--|| AggregationKind: isKindOf

%%Device }|--|| DeviceType: deviceSubClass
Device }|--|| DeviceKind: hasDeviceKind

Device }o--o| Building: contains

Device }o--o| BuildingSpace: contains

BuildingSpace }o--|| Building: hasSpace

BuildingSpace }o--|| BuildingSpace: hasSpace

Building }o--|| Site: hasSite

Organisation }|--o| Site: isOwnedBy

Address }|--o| Site: hasAddress

%% isLocationOf is the inverse of hasLocation
Building }o--|| Location: isLocationOf

WeatherArea }o--|| Location: isLocationOf

Device }o--|| Location: isLocationOf

BuildingSpace }o--|| Location: isLocationOf

%% isAddressOf is the inverser of hasAddress
Building }o--|| Address: isAddressOf

%% isAddressOf is the inverser of hasAddress
Organisation }o--o| Address: isAddressOf

Site ||--|| Location: hasLocation

Organisation }|--o{ Building: owns

Organisation }|--o{ BuildingSpace: owns

Relations {
    %% All relationships in this model will be physically represented as rows in this table
    uuid entityFrom
    uuid entityTo
    str type
    str entity_classFrom
    str entity_classTo
    %% 1 = active, 0 = not active
    int isActive 
    %% Examples:
    %% <uuid1>, <uuid2>, contains, Building, BuildingSpace, 1
    %% <uuid1>, <uuid2>, owns, Organisation, Building, 1
}

Building {
    %% Should have the same attributes as https://github.com/smart-data-models/dataModel.S4BLDG/Building/schema.json
    %% Note that any nested values should be represented as relationships
    %% Owner should simply reference an Organisation thorugh relations table
    uuid id PK
    %% areaServed is still unclear and untillk further clarified, should simply be Null
    string areaServed
    %% Type must be Building. This is mandatory
    string type
    string name
    string description
    %% The following two attributes are extra compared to Smart Data Models
    float livingArea
    float businessArea
    string source
    string dateCreated
    string dateModified
}

BuildingSpace {
    %% Should have the same attributes as https://github.com/smart-data-models/dataModel.S4BLDG/BuildingSpace/schema.json
    %% Attributes not included:
    %% bounds: Not yet included because it is unclear how it should relate to the coordinate system, but most likely there will be a coordinate table with relation to a CoordinateSystem table
    %% Owner should simply reference an Organisation thorugh relations table
    %% dataProvider should simply reference an Organisation thorugh relations table
    %% address should be a relation to the address table
    %% isSpaceOfBuilding is defined via relationships "hasSpace"
    %% seeAlso may be added later as a relation to extra material

    uuid id PK
    %% areaServed is still unclear and untillk further clarified, should simply be Null
    string areaServed
    %% type: Must be BuildingSpace. This is mandatory
    string type
    string name
    string alternateName
    %% airVolume: in m3
    float airVolume
    %% area: in m2
    float area
    string description
    %% BuildingSpace can be 1 of [ BuildingElementProxy, BuildingStorey, Column, Covering, CurtainWall, Door, OpeningElement, Plate, Railing, Roof, Site, Slab, Space, Stair, StairFlight, Storey, Wall, WallStandardCase, Window ]
    string buildingSpaceKind
    string source
    %%dataProvider: A sequence of characters identifying the provider of the harmonised data entity. Example from Github "IFC file"
    string dataProvider
    string dateCreated
    string dateModified
}

Organisation {
    uuid id PK
    string type
    string name
    string description
    string contactPoint
    string source
    string dateCreated
    string dateModified
}

Site {
    uuid id PK
    string type
    string name
    string description
    string source
    string dateCreated
    string dateModified
}

Location {
    uuid id PK
    string type
    string value
    string source
    string dateCreated
    string dateModified
}

Address {
    %% Defined according to the address field in this https://github.com/smart-data-models/dataModel.S4BLDG/BuildingSpace/schema.json
    %% For examples see https://schema.org/address
    %% 
    uuid id PK
    %% type: should be PostalAddress. Is mandatory
    string type
    %% streetAddress: Is mandatory
    string streetAddress
    string streetNr
    string addressLocality
    string addressRegion
    string postalCode
    string addressCountry
    string postOfficeBoxNumber
    %% district: A district is a type of administrative division that, in some countries, is managed by the local government
    string district
    string source
    string dateCreated
    string dateModified
}

PropertyValue {
    %% Foreign key can map to any subclass of ProcedureExecution
    uuid procedureExecution FK
    float value
    %% datetime: is always either the specific time of the observation or the end time where the value is for instance averaged since the last time. It should be a xsd datetime
    datetime timestamp
    string source
    string dateCreated
    string dateModified
}

PropertyValueInterval {
    %% Foreign key can map to any subclass of ProcedureExecution
    uuid procedureExecution FK
    float value
    %% startTime: the beginning of the interval. It should be an xsd datetime
    datetime startTime
    %% endTime: the beginning of the interval. It should be an xsd datetime
    datetime endTime
    string source
    string dateCreated
    string dateModified
}

AccumulationKind {
    %% https://zepben.github.io/evolve/docs/cim/cim100/TC57CIM/IEC61968/Metering/AccumulationKind/
    uuid id PK
    str name
}

AggregationKind {
    %% https://zepben.github.io/evolve/docs/cim/cim100/TC57CIM/IEC61968/Metering/AggregateKind
    uuid id PK
    str name
}

Property {
    %% Defined by https://www.qudt.org/doc/DOC_VOCAB-QUANTITY-KINDS.html#Instances
    uuid id PK
    %% type: Must be Property. Mandatory
    string type
    %% name: should correspond to the name after quantityKind: in the associated link
    string name
    string description
    string dateCreated
    string dateModified
}

UnitOfMeasure {
    %% Valid values for unit of measure are provided in https://qudt.org/2.1/vocab/unit as Individuals. So further information should be looked up there. A more readable lookup may be found here: https://www.qudt.org/doc/DOC_VOCAB-UNITS.html
    uuid id
    %% name: This should correspond to the name after unit: in the readable lookup
    string name
    string description
    string dateCreated
    string dateModified
}

Observation {
    %% Subclass of ProcedureExecution
    %% This maps devices with multiple timeseries based on property and unit of measure. Example is a district heating meter, which measures forward temperature and return temperature, i.e. the property is Temperature and the unit of measure is celsius.
    uuid id PK
    string type
    string description
    string dateCreated
    string dateModified
    %% temporalType: should be Interval or Instant. Property values for instant types are stored in PropertyValue
    string temporalType
}

Meter {
    %% Subclass of device and has all the relations a device has
    uuid id PK
    %% type: Must be Meter. Is mandatory
    string type
    string name
    string description
    string dateInstalled
    string status
    string source
    %% The dates below refer to creation and modification in this model
    string dateCreated
    string dateModified
}

DeviceKind {
    %% Kinds of devices describe models of devices, with common properties having the same value, common states being the same, common functions, and common services.
    uuid id PK
    %% type: Must be DeviceKind
    string type
    string modelName
    string manufacturer
    string description
    string dateCreated
    string dateModified
}

ForecastModel {
    uuid id PK
    string name
    string modelType
    string modelVersion
    %% ISO 8601 compliant
    string period
    %% ISO 8601 compliant, how often forecasts are delivered. If empty, frequency is varying.
    string frequency
    %% ISO 8601 compliant, time interval between forecast values. If empty, interval is varying.
    string interval
    %% Free text field describing the forecast model
    string description
    string dateCreated
    string dateModified
}

ForecastObservation {
    uuid id PK
    string type
    datetime dateCreated
    datetime dateModified
}

ForecastData {
    %% Foreign key of forecast
    uuid procedureExecution FK
    float value
    datetime timestamp
    %% version: Must be an integer. For each (procedureExecution, timestamp) pair, a new forecast value must have a strictly higher version than any previous value for that timestamp.
    int version
}

WeatherArea {
    uuid id PK
    string name
    string description
    string dateCreated
    string dateModified
}

WeatherArea }o--|| Site: hasSite
Device }o--o| WeatherArea: contains
ForecastModel }|--|| WeatherArea: hasForecast

State {
    uuid id PK
    string type
    string name
    string description
    string dateCreated
    string dateModified
}

StateValue {
    uuid stateId FK
    float value
    datetime timestamp
    string dateCreated
    string dateModified
}

%% Relationships for State
State }|--|| Device: isStateOf
State }|--|| Building: isStateOf
State }|--|| BuildingSpace: isStateOf
State }|--|| Site: isStateOf
State }|--|| WeatherArea: isStateOf
StateValue }|--|| State: hasState

State }|--|| UnitOfMeasure: isUnitOf

State }|--|| Property: isForecastedBy

State }|--|| AccumulationKind: isKindOf

State }|--|| AggregationKind: isKindOf
````
