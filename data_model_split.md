# Data Model — Focused Diagrams

This file contains focused sub-diagrams derived from the full data model. Each diagram centres on one concern. Referenced entities from other diagrams are shown with abbreviated attributes.

## Table of Contents

1. [Device and Direct Relations](#1-device-and-direct-relations)
2. [District Heating System](#2-district-heating-system)
3. [Device Subclasses](#3-device-subclasses)
4. [Observations and Timeseries](#4-observations-and-timeseries)
5. [Buildings and Spaces](#5-buildings-and-spaces)
6. [State](#6-state)
7. [Forecast](#7-forecast)
8. [WeatherArea](#8-weatherarea)
9. [Site](#9-site)

---

## 1. Device and Direct Relations

Focus: Device, DeviceKind, and the structural entities a Device is physically placed in or measured by.
For timeseries/observations see [diagram 4](#4-observations-and-timeseries). For forecasts on a device see [diagram 7](#7-forecast). For state on a device see [diagram 6](#6-state).

````mermaid
erDiagram

Device }|--o| DeviceKind: hasDeviceKind
Device }o--o| Building: contains
Device }o--o| BuildingSpace: contains
Device }o--o| WeatherArea: contains
Device }o--o| Site: contains
Device }o--o| Location: isLocationOf
Organisation ||--o{ Device: owns
Device }o--|| Shunt: primaryMeasuredBy
Device }o--|{ Shunt: secondaryMeasuredBy
Device ||--o{ HeatingZone: measuresFor
HeatingZone |o--o{ Device: contains

Device {
    uuid id PK
    string type
    string name
    string description
    string status
    string source
    datetime dateCreated
    datetime dateModified
}

DeviceKind {
    uuid id PK
    string type
    string modelName
    string manufacturer
    string description
    datetime dateCreated
    datetime dateModified
}

Building {
    uuid id PK
    string name
}

BuildingSpace {
    uuid id PK
    string name
}

WeatherArea {
    uuid id PK
    str name
}

Location {
    uuid id PK
    string type
    geojson value
}

Organisation {
    uuid id PK
    string name
}

Shunt {
    uuid id PK
    string note
    int commissioningYear
    string meterSize
}

HeatingZone {
    uuid id PK
    string name
}

Site {
    uuid id PK
    string name
}
````

---

## 2. District Heating System

Focus: DistrictHeatingSystem, Shunt, HeatingZone and HeatingZoneAggregate.
HeatingZoneAggregate is a subclass of Device — its `id` aligns with a Device record.
For forecasts see [diagram 7](#7-forecast). For state see [diagram 6](#6-state).

````mermaid
erDiagram

Site ||--o{ DistrictHeatingSystem: contains
DistrictHeatingSystem |o--o{ Shunt: contains
Device }o--|| Shunt: primaryMeasuredBy
Device }o--|{ Shunt: secondaryMeasuredBy
HeatingZone }o--|| Shunt: belongsToShunt
HeatingZone |o--o{ Device: contains
Device ||--o{ HeatingZone: measuresFor
HeatingZone }o--o{ WeatherArea: hasWeatherArea

%% HeatingZoneAggregate is a subclass of Device — shares the same id
Device ||--o| HeatingZoneAggregate: isA

Site {
    uuid id PK
    string name
}

DistrictHeatingSystem {
    uuid id PK
    string type
    string name
    string description
    string source
    datetime dateCreated
    datetime dateModified
}

Shunt {
    uuid id PK
    string note
    int commissioningYear
    boolean isConnectedToMainNetwork
    string firstSroDataDate
    string reducedToCurrentTempDate
    int dnAfterShunt
    int dnAtConnectionPoint
    float electricPowerKw
    float heatPowerWinterPeakKw
    float heatPowerSummerPeakKw
    float pipeLengthAfterShuntM
    string eloverblikMeteringPointId
    string meterSize
}

HeatingZone {
    uuid id PK
    string type
    string name
    string description
    string source
    datetime dateCreated
    datetime dateModified
}

HeatingZoneAggregate {
    %% Subclass of Device — id aligns with Device.id
    uuid id PK
    string aggregationMethod
    string calculationVersion
    string expectedUpdateFrequency
    int inputDeviceCount
}

Device {
    uuid id PK
    string type
    string name
}

WeatherArea {
    uuid id PK
    str name
}
````

---

## 3. Device Subclasses

Focus: All known subclasses of Device. Each subclass shares the same `id` as its Device record (one-to-one extension).
WeatherStation and HVAC are listed as subclasses but are not yet fully defined in the model.

````mermaid
erDiagram

Device ||--o| Meter: isA
Device ||--o| Sensor: isA
Device ||--o| Photovoltaic: isA
Device ||--o| EV: isA
Device ||--o| HeatingZoneAggregate: isA

Device {
    uuid id PK
    string type
    string name
    string description
    string status
    string source
    datetime dateCreated
    datetime dateModified
}

Meter {
    uuid id PK
    datetime dateInstalled
}

Sensor {
    uuid id PK
    datetime dateInstalled
}

Photovoltaic {
    uuid id PK
    float installedPeakPower
    float inverterCapacity
    float inverterVoltageRating
    float systemLoss
    string installationPosition
    float installedArea
    string dateInstalled
    float installationCost
    int expectedLifetime
    string mountType
    float slope
    float azimuth
    datetime dateCreated
    datetime dateModified
}

EV {
    uuid id PK
    float powerCharge
    datetime dateInstalled
}

HeatingZoneAggregate {
    uuid id PK
    string aggregationMethod
    string calculationVersion
    string expectedUpdateFrequency
    int inputDeviceCount
}
````

---

## 4. Observations and Timeseries

Focus: Observation, PropertyValue, PropertyValueInterval and the lookup tables they reference.
The `PropertyValueInterval` relation to Observation exists in the model but is not yet active (commented out in the master diagram).

````mermaid
erDiagram

PropertyValue }|--|| Observation: hasResult
%%PropertyValueInterval }|--|| Observation: hasResult

Observation }|--|| Device: madeExecution
Observation }|--|| Property: isObservedBy
Observation }|--|| UnitOfMeasure: isUnitOf
Observation }|--|| AccumulationKind: isKindOf
Observation }|--|| AggregationKind: isKindOf

Observation {
    uuid id PK
    string type
    string description
    datetime dateCreated
    datetime dateModified
    %% temporalType: Interval or Instant
    string temporalType
}

PropertyValue {
    uuid procedureExecution FK
    float value
    %% Specific time of observation or end time of interval
    datetime timestamp
    string source
}

PropertyValueInterval {
    uuid procedureExecution FK
    float value
    datetime startTime
    datetime endTime
    string source
}

Device {
    uuid id PK
    string type
    string name
}

Property {
    uuid id PK
    string type
    string name
    string description
    datetime dateCreated
    datetime dateModified
}

UnitOfMeasure {
    uuid id
    string name
    string description
    datetime dateCreated
    datetime dateModified
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
````

---

## 5. Buildings and Spaces

Focus: Building, BuildingSpace and their immediate relations.
For forecasts see [diagram 7](#7-forecast). For state see [diagram 6](#6-state).

````mermaid
erDiagram

BuildingSpace }o--|| Building: hasSpace
BuildingSpace }o--|| BuildingSpace: hasSpace
Building }o--|| Site: hasSite
Building }o--|| Location: isLocationOf
Building }o--|| Address: isAddressOf
BuildingSpace }o--|| Location: isLocationOf
Organisation }|--o{ Building: owns
Organisation }|--o{ BuildingSpace: owns
Organisation }o--o| Address: isAddressOf
Device }o--o| Building: contains
Device }o--o| BuildingSpace: contains

Building {
    %% https://github.com/smart-data-models/dataModel.S4BLDG/Building/schema.json
    uuid id PK
    string areaServed
    string type
    string name
    string description
    float livingArea
    float businessArea
    string source
    datetime dateCreated
    datetime dateModified
}

BuildingSpace {
    %% https://github.com/smart-data-models/dataModel.S4BLDG/BuildingSpace/schema.json
    uuid id PK
    string areaServed
    string type
    string name
    string alternateName
    float airVolume
    float area
    string description
    string buildingSpaceKind
    string source
    string dataProvider
    datetime dateCreated
    datetime dateModified
}

Site {
    uuid id PK
    string name
}

Location {
    uuid id PK
    string type
    geojson value
}

Address {
    uuid id PK
    string type
    string streetAddress
    string streetNr
    string addressLocality
    string addressRegion
    string postalCode
    string addressCountry
    string postOfficeBoxNumber
    string district
    string source
    datetime dateCreated
    datetime dateModified
}

Organisation {
    uuid id PK
    string type
    string name
}

Device {
    uuid id PK
    string type
    string name
}
````

---

## 6. State

Focus: State and StateValue — current/historical state values for physical and spatial entities.

````mermaid
erDiagram

StateValue }|--|| State: hasState
State }|--|| Device: isStateOf
State }|--|| Building: isStateOf
State }|--|| BuildingSpace: isStateOf
State }|--|| Site: isStateOf
State }|--|| WeatherArea: isStateOf
State }|--|| Shunt: isStateOf
State }|--|| HeatingZone: isStateOf
State }|--|| Property: isPropertyOf
State }|--|| UnitOfMeasure: isUnitOf
State }|--|| AccumulationKind: isKindOf
State }|--|| AggregationKind: isKindOf

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
}

Device {
    uuid id PK
    string name
}

Building {
    uuid id PK
    string name
}

BuildingSpace {
    uuid id PK
    string name
}

Site {
    uuid id PK
    string name
}

WeatherArea {
    uuid id PK
    str name
}

Shunt {
    uuid id PK
    string meterSize
}

HeatingZone {
    uuid id PK
    string name
}

Property {
    uuid id PK
    string name
}

UnitOfMeasure {
    uuid id
    string name
}

AccumulationKind {
    uuid id PK
    str name
}

AggregationKind {
    uuid id PK
    str name
}
````

---

## 7. Forecast

Focus: ForecastModel, ForecastObservation and ForecastData.

````mermaid
erDiagram

ForecastObservation }|--|| ForecastModel: madeExecution
ForecastData }|--|| ForecastObservation: hasResult
ForecastModel }|--|| Device: hasForecast
ForecastModel }|--|| Building: hasForecast
ForecastModel }|--|| BuildingSpace: hasForecast
ForecastModel }|--|| Site: hasForecast
ForecastModel }o--|| Shunt: hasForecast
ForecastModel }o--|| HeatingZone: hasForecast
ForecastModel }|--|| WeatherArea: hasForecast
ForecastObservation }|--|| UnitOfMeasure: isUnitOf
ForecastObservation }|--|| Property: isForecastedBy
ForecastObservation }|--|| AccumulationKind: isKindOf
ForecastObservation }|--|| AggregationKind: isKindOf

ForecastModel {
    uuid id PK
    string name
    string modelType
    string modelVersion
    %% ISO 8601 duration — forecast horizon
    string period
    %% ISO 8601 duration — how often forecasts are delivered
    string frequency
    %% ISO 8601 duration — time step between forecast values
    string interval
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
    uuid procedureExecution FK
    float value
    datetime timestamp
    %% Strictly increasing per (procedureExecution, timestamp) pair
    int version
}

Device {
    uuid id PK
    string name
}

Building {
    uuid id PK
    string name
}

BuildingSpace {
    uuid id PK
    string name
}

Site {
    uuid id PK
    string name
}

Shunt {
    uuid id PK
    string meterSize
}

HeatingZone {
    uuid id PK
    string name
}

WeatherArea {
    uuid id PK
    str name
}

Property {
    uuid id PK
    string name
}

UnitOfMeasure {
    uuid id
    string name
}

AccumulationKind {
    uuid id PK
    str name
}

AggregationKind {
    uuid id PK
    str name
}
````

---

## 8. WeatherArea

Focus: WeatherArea and the entities it connects to.
For forecasts see [diagram 7](#7-forecast). For state see [diagram 6](#6-state).

````mermaid
erDiagram

WeatherArea }o--|{ Site: hasSite
Device }o--o| WeatherArea: contains
WeatherArea }o--|| Location: isLocationOf
HeatingZone }o--o{ WeatherArea: hasWeatherArea

WeatherArea {
    uuid id PK
    string name
    string description
    string dateCreated
    string dateModified
}

Site {
    uuid id PK
    string name
}

Device {
    uuid id PK
    string type
    string name
}

Location {
    uuid id PK
    string type
    geojson value
}

HeatingZone {
    uuid id PK
    string name
}
````

---

## 9. Site

Focus: Site and its immediate relations.
For forecasts see [diagram 7](#7-forecast). For state see [diagram 6](#6-state).

````mermaid
erDiagram

Organisation }|--o| Site: isOwnedBy
Address }|--o| Site: hasAddress
Site ||--|| Location: hasLocation
Site ||--o{ DistrictHeatingSystem: contains
Site ||--o{ Device: contains
WeatherArea }o--|{ Site: hasSite
Building }o--|| Site: hasSite

Site {
    uuid id PK
    string type
    string name
    string description
    string source
    datetime dateCreated
    datetime dateModified
}

Organisation {
    uuid id PK
    string type
    string name
    string contactPoint
}

Address {
    uuid id PK
    string type
    string streetAddress
    string streetNr
    string addressLocality
    string postalCode
    string addressCountry
}

Location {
    uuid id PK
    string type
    geojson value
}

DistrictHeatingSystem {
    uuid id PK
    string name
}

WeatherArea {
    uuid id PK
    str name
}

Building {
    uuid id PK
    string name
}

Device {
    uuid id PK
    string type
    string name
}
````
