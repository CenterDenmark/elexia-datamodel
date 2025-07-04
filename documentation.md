# Data Model Documentation

## Table of Contents

- [Data Model Documentation](#data-model-documentation)
  - [Table of Contents](#table-of-contents)
  - [How to Read the Data Model](#how-to-read-the-data-model)
  - [Clarifications for Static Entities](#clarifications-for-static-entities)
    - [Property](#property)
    - [UnitOfMeasure](#unitofmeasure)
    - [AccumulationKind](#accumulationkind)
    - [AggregationKind](#aggregationkind)
  - [Entities](#entities)
    - [Building](#building)
    - [BuildingSpace](#buildingspace)
    - [Device](#device)
    - [Observation](#observation)
    - [ForecastModel](#forecastmodel)
    - [ForecastObservation](#forecastobservation)
    - [ForecastData](#forecastdata)
    - [Address](#address)
    - [Site](#site)
    - [Location](#location)
    - [Organisation](#organisation)
    - [PropertyValue](#propertyvalue)
    - [Relations](#relations)
    - [WeatherArea](#weatherarea)
    - [State](#state)
    - [StateValue](#statevalue)
  - [API Reference](#api-reference)

This documentation describes the physical datasets of the data model, including the purpose and structure of each entity, relationships, and specific guidelines for defining properties and units of measure. This is intended for sharing on GitHub and provides complete details for all entities.

---

## How to Read the Data Model

- **Entities**: Each entity (e.g., `Building`, `Device`, `Observation`) represents a distinct type of object with specific attributes.

- **Relationships**:

  - All relationships (except those involving `PropertyValue` and `PropertyValueInterval`) are stored in a dedicated `Relations` table. Each row in the `Relations` table specifies:

    - `entityFrom`: The UUID of the source entity.
    - `entity_classFrom`: The type of the source entity (e.g., `Building`, `Device`).
    - `entityTo`: The UUID of the target entity.
    - `entity_classTo`: The type of the target entity (e.g., `BuildingSpace`, `Observation`).
    - `type`: The type of relationship (e.g., `contains`, `owns`).

  - Relationships are illustrated in Crow's Foot notation in diagrams to indicate whether a relationship is mandatory or optional.

  Example of a `Relations` entry:

  ```
  {
  "entityFrom": "Building123", "entity_classFrom": "Building"
  "entityTo": "Space456", "entity_classTo": "BuildingSpace"
  "type": "hasSpace"
  }
  ```

- **Timestamps**: All timestamps follow the XSD datetime standard (ISO8601 format).

- **Attributes**: Each entity's attributes are described below, with references where applicable.
- **Ids**: All id columns should contain UUID to ensure uniqueness in values.
- **Primary keys**: For all non-timeseries datasets the primary key of the dataset consists of ["id","dateModified"]. This is because updates are appended as described in (#api-reference).

---

## Clarifications for Static Entities

Static entities contain definitions that apply globally in the data model and cannot be changed by any other stakeholder than the platform owner. These include Property, UnitOfMeasure, AccumulationKind, and AggregationKind. All references to these entities must use the values provided by the platform.

### Property

A `Property` represents a measurable or observable attribute, such as temperature, humidity, or energy consumption.

- **Definition**:
  - **QUDT Quantity Kinds**: Properties are based on the [QUDT Quantity Kinds vocabulary](https://www.qudt.org/doc/DOC_VOCAB-QUANTITY-KINDS.html#Instances).

- **Attributes**:
  - `id`: Unique identifier.
  - `type`: Always "Property".
  - `name`: Descriptive name.
  - `description`: Additional context.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Example**:
```json
{
  "id": "Property001",
  "type": "Property",
  "name": "Temperature",
  "description": "Thermal state of an object",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

### UnitOfMeasure

Represents the unit used to measure a `Property`.

- **Definition**:
  - Units must be based on the [QUDT Units vocabulary](https://www.qudt.org/doc/DOC_VOCAB-UNITS.html).

- **Attributes**:
  - `id`: Unique identifier.
  - `name`: Name based on QUDT Units.
  - `description`: Unit details.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Example**:
```json
{
  "id": "Unit001",
  "name": "DEG_C",
  "description": "Degrees Celsius",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

### AccumulationKind

Defines the kind of accumulation for a property or state (e.g., none, sum, average, delta). Defined based on [CIM AccumulationKind](https://zepben.github.io/evolve/docs/cim/cim100/TC57CIM/IEC61968/Metering/AccumulationKind/).

- **Attributes**:
  - `id*`: Unique identifier.
  - `name`: Name of the accumulation kind (e.g., "None", "Sum", "Average", "Delta").

- **Example**:
```json
{
  "id": "ba5745a5-9633-406f-ac8c-6f9e4aa0cfd8",
  "name": "None"
}
```

### AggregationKind

Defines the kind of aggregation for a property or state (e.g., none, average, min, max). Defined based on [CIM AggregationKind](https://zepben.github.io/evolve/docs/cim/cim100/TC57CIM/IEC61968/Metering/AggregateKind).

- **Attributes**:
  - `id*`: Unique identifier.
  - `name`: Name of the aggregation kind (e.g., "None", "Average", "Min", "Max").

- **Example**:
```json
{
  "id": "e16765d1-e91f-4c57-9e5e-41a2ea2be0fd",
  "name": "None"
}
```

---

## Entities

### Building

Represents a physical structure.

- **Attributes**:
  - `id*`: Unique identifier.
  - `type*`: Always "Building".
  - `name`: Building name.
  - `description`: Metadata.
  - `livingArea`: Area in square meters.
  - `businessArea`: Area in square meters.
  - `source`: Data source.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Reference**:

  - Based on attributes from [Smart Data Models S4BLDG/Building](https://github.com/smart-data-models/dataModel.S4BLDG/blob/master/Building/schema.json).

- **Example**:
```json
{
  "id": "Building123",
  "type": "Building",
  "name": "Office Building A",
  "livingArea": 1000.0,
  "businessArea": 500.0,
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

---

### BuildingSpace

Defines spatial elements of a building and supports hierarchical relationships.

- **Attributes**:
  - `id*`: Unique identifier.
  - `type*`: Always "BuildingSpace".
  - `name`: Space name.
  - `alternateName`: Alternative name.
  - `area`: Area in square meters.
  - `airVolume`: Volume in cubic meters.
  - `buildingSpaceKind*`: Type of space. This can be any of those in the reference.
  - `source`: Data source.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Reference**:

  - Based on attributes from [Smart Data Models S4BLDG/BuildingSpace](https://github.com/smart-data-models/dataModel.S4BLDG/blob/master/BuildingSpace/schema.json).

- **Example**:
```json
{
  "id": "Space001",
  "type": "BuildingSpace",
  "name": "Conference Room",
  "area": 50.0,
  "airVolume": 150.0,
  "buildingSpaceKind": "Space",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

---

### Device

Represents a physical device.

- **Attributes**:
  - `id*`: Unique identifier.
  - `type*`: Device type (e.g., "Meter").
  - `name`: Device name.
  - `description`: Metadata.
  - `dateInstalled`: Installation date.
  - `status`: Device status.
  - `source`: Data source.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Reference**:

  - Based on [SAREF Device](https://saref.etsi.org/core/v3.2.1/#saref\:Device).

- **Example**:
```json
{
  "id": "Device123",
  "type": "Meter",
  "name": "Electric Meter",
  "description": "Measures electricity usage",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

---

### Observation

Represents a measurable event where a `Device` measures a specific `Property`.

- **Attributes**:
  - `id*`: Unique identifier.
  - `type*`: Always "Observation".
  - `description`: Metadata.
  - `temporalType`: Interval or Instant.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Example**:
```json
{
  "id": "Obs001",
  "type": "Observation",
  "description": "Temperature measurement",
  "temporalType": "Instant",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

---

### ForecastModel

Represents a forecasting model that produces forecasted values for properties at sites, buildings, building spaces or devices.

- **Attributes**:
  - `id*`: Unique identifier.
  - `name`: Model name.
  - `modelType`: Type of model (e.g., "ARIMA", "ML").
  - `modelVersion`: Version string.
  - `period`: ISO 8601 period string indicating the period that each forecast will cover (e.g., "PT3H" for a 3-hour forecast horizon).
  - `frequency`: ISO 8601 period string specifying how often forecasts are delivered. If left empty, the frequency is assumed to be varying.
  - `interval`: ISO 8601 period string specifying the time interval between forecast values. If left empty, the interval is assumed to be varying.
  - `description`: Free text field describing the forecast model.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Example**:
```json
{
  "id": "forecast_weather_v3",
  "organisationName": "dataprovider_v3",
  "name": "Weather Forecast Model v3",
  "modelType": "ML",
  "modelVersion": "1.0.0",
  "period": "PT3H",
  "frequency": "PT1H",
  "interval": "PT1H",
  "description": "Forecasts temperature and humidity every hour for the next 3 hours.",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

--- 

### ForecastObservation

Represents a forecasted observation for a property, produced by a forecast model.

- **Attributes**:
  - `id*`: Unique identifier.
  - `type*`: Always "ForecastObservation".
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.
  - `propertyId`, `unitOfMeasureId`, `accumulationKindId`, `aggregationKindId`: As defined in the ForecastModel's properties array.

- **Note**: Normally, forecast observations are created automatically when posting a ForecastModel with a `properties` array.

- **Example**:
```json
{
  "id": "ForecastObs001",
  "type": "ForecastObservation",
  "propertyId": "8a4e4cac-e568-4b88-8955-e04f65d81263",
  "unitOfMeasureId": "d98e286b-f437-4375-9bbd-ef8cfbc54cb3",
  "accumulationKindId": "15e95acc-48b7-4756-a264-c0cac5461125",
  "aggregationKindId": "f5218a15-9b22-4cc5-94db-4e106bae169d",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

---

### ForecastData

Stores the forecasted values for a forecast observation.

- **Attributes**:
  - `procedureExecution*`: Linked forecast observation.
  - `value*`: Forecasted value.
  - `timestamp*`: Forecasted time.
  - `version*`: Integer version. For each (`procedureExecution`, `timestamp`) pair, a new forecast value must have a strictly higher version than any previous value for that timestamp.

- **Example**:
```json
{
  "procedureExecution": "ForecastObs001",
  "value": 18.5,
  "timestamp": "2024-01-02T12:00:00Z",
  "version": 1
}
```

> **Note:** If a new forecast value is delivered for a specific `procedureExecution` and `timestamp`, the `version` must be incremented (it must be strictly higher than the previous value for that timestamp).

---

### Address

Defines a postal address associated with entities like `Building` or `Organisation`.

- **Definition**

  - The entity is defined based on [https://schema.org/address](https://schema.org/address).

- **Attributes**:
  - `id*`: Unique identifier.
  - `type*`: Always "PostalAddress".
  - `streetAddress*`: Street name.
  - `streetNr`: Street number.
  - `addressLocality`: Locality.
  - `postalCode`: Postal code.
  - `addressCountry`: Country.
  - `district`: District.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Example**:
```json
{
  "id": "Address001",
  "type": "PostalAddress",
  "streetAddress": "123 Main St",
  "streetNr": "456",
  "addressLocality": "Springfield",
  "postalCode": "62704",
  "addressCountry": "USA",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

---

### Site

Represents a geographic or administrative area associated with buildings or organisations. In the Elexia project, this would be a Pilot Site.

- **Attributes**:
  - `id*`: Unique identifier.
  - `type*`: Always "Site".
  - `name`: Site name.
  - `description`: Metadata.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Example**:
```json
{
  "id": "Site001",
  "type": "Site",
  "name": "Industrial Park",
  "description": "Industrial development zone",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

---

### Location

Defines a spatial location associated with other entities.
 
- **Attributes**:
  - `id*`: Unique identifier.
  - `type*`: Always "Location".
  - `value`: A GeoJSON string representing the location.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Example**:
```json
{
  "id": "Location001",
  "type": "Location",
  "value": "{\"type\": \"Point\", \"coordinates\": [55.6761, 12.5683]}",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

---

### Organisation

Represents an organisation that owns or manages entities such as `Building` or `Site`.

- **Attributes**:
  - `id*`: Unique identifier.
  - `type*`: Always "Organisation".
  - `name`: Organisation name.
  - `description`: Metadata.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Example**:
```json
{
  "id": "Org001",
  "type": "Organisation",
  "name": "Green Energy Co.",
  "description": "Renewable energy solutions",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

---

### PropertyValue

Stores the results of an observation.

> Note that data is not cleaned for duplicates. Upon multiple deliveries of the same timeseries values duplicates will occur.

- **Attributes**:
  - `procedureExecution*`: Linked observation.
  - `value*`: Measured value.
  - `timestamp*`: Measurement time.

- **Example**:
```json
{
  "procedureExecution": "Obs001",
  "value": 22.5,
  "timestamp": "2024-01-01T12:00:00Z"
}
```

---

### Relations

Defines relationships between entities.

- **Attributes**:
  - `entityFrom`: Source entity.
  - `entity_classFrom`: Source class.
  - `entityTo`: Target entity.
  - `entity_classTo`: Target class.
  - `type`: Relationship type.
  - `isActive`: Integer (1 = active, 0 = not active) indicating if the relationship is currently active.

**Example**:
```json
{
  "entityFrom": "Device123",
  "entity_classFrom": "Device",
  "entityTo": "Obs001",
  "entity_classTo": "Observation",
  "type": "madeExecution",
  "isActive": 1
}
```

---

### WeatherArea

Represents a weather area, which is a spatial region associated with a site. Weather areas can have devices and forecasts connected to them, similar to buildings.

- **Attributes**:
  - `id*`: Unique identifier.
  - `name`: Weather area name.
  - `description`: Metadata.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Example**:
```json
{
  "id": "WeatherArea001",
  "name": "Rooftop Weather Area",
  "description": "Weather station area on the rooftop",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

---

### State

Represents a set point or control state that can be associated with a device or any location entity (Building, BuildingSpace, Site, WeatherArea). Not used for forecasts.

- **Attributes**:
  - `id*`: Unique identifier.
  - `type*`: Always "State".
  - `name`: State name.
  - `description`: Metadata.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Relations**:
  - Linked to exactly one of: Device, Building, BuildingSpace, Site, or WeatherArea.
  - Linked to one Property (what the state controls), one UnitOfMeasure, one AccumulationKind, and one AggregationKind via relations.

- **Example**:
```json
{
  "id": "State001",
  "type": "State",
  "name": "Thermal Resistance Setpoint",
  "description": "Target thermal resistance for shared wall",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

### StateValue

Stores the set point or control value for a state. At any given time, the value of a state is defined by the latest value in StateValue before the specified timestamp.

- **Attributes**:
  - `stateId*`: Linked state.
  - `value*`: Set point value.
  - `timestamp*`: Time of the set point.
  - `source`: Data source.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Example**:
```json
{
  "stateId": "State001",
  "value": 21.0,
  "timestamp": "2024-01-02T12:00:00Z",
  "dateCreated": "2024-01-02T12:00:00Z",
  "dateModified": "2024-01-02T12:00:00Z"
}
```

> **Note:** There is an endpoint for obtaining the last value of a state before a specific timestamp.

---

## API Reference

For interacting with the data model, it is reccomended to use the tranformation endpoints, but each entity also has its own base endpoint for direct interaction. Use this diagram as reference:

![d1](/res/API_interaction_diagram.drawio.png)

- For guide on how to add data, please refer to [this guide](/guides/data%20provider/transformation_endpoints_guide.md) 
- For more info on how base endpoints work, refer to [this guide](/guides/base_endpoints_guide.md)

**Swagger Documentation**: 
  
  - [Base Endpoints](https://api.centerdenmark.com/swagger-ui/index.html#/)
  - [Transformation Endpoints for Prod environment](https://transform.centerdenmark.com/swagger-ui/index.html#/)
  - [Transformation Endpoints for Test environment](https://test-transform.centerdenmark.com/swagger-ui/index.html#/)

**Portal**: [User interface for API](https://portal.centerdenmark.com/)

An API for mapping between existing ids and data lake ids is being developed [here](https://transform.centerdenmark.com/swagger-ui/index.html). This api also allows posting complex json objects such that the user does not need to post individual entities to each table. 

---
