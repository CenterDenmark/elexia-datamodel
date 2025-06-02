# Data Model Documentation

## Table of Contents

- [Data Model Documentation](#data-model-documentation)
  - [Table of Contents](#table-of-contents)
  - [How to Read the Data Model](#how-to-read-the-data-model)
  - [Clarifications for Properties and Units of Measure](#clarifications-for-properties-and-units-of-measure)
    - [Property](#property)
    - [UnitOfMeasure](#unitofmeasure)
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
  - [API Reference](#api-reference)
  - [MQTT Reference (WIP)](#mqtt-reference-wip)

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

## Clarifications for Properties and Units of Measure

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
```
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

Represents a forecasting model that produces forecasted values for properties at sites, buildings, or devices.

- **Attributes**:
  - `id*`: Unique identifier.
  - `name`: Model name.
  - `modelType`: Type of model (e.g., "ARIMA", "ML").
  - `modelVersion`: Version string.
  - `period`: ISO 8601 period string for forecast interval.

- **Example**:
```json
{
  "id": "ForecastModel001",
  "name": "WeatherForecastModel",
  "modelType": "ML",
  "modelVersion": "1.0.0",
  "period": "P1D"
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

- **Example**:
```json
{
  "id": "ForecastObs001",
  "type": "ForecastObservation",
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


**Example**:
```json
{
  "entityFrom": "Device123",
  "entity_classFrom": "Device",
  "entityTo": "Obs001",
  "entity_classTo": "Observation",
  "type": "madeExecution"
}
```

---

## API Reference

For interacting with the data model, it is reccomended to use the tranformation endpoints, but each entity also has its own base endpoint for direct interaction. Use this diagram as reference:

![d1](/res/API_interaction_diagram.drawio.png)

- For guide on how to add data, please refer to [this guide](/guides/data%20provider/transformation_endpoints_guide.md) 
- For more info on how base endpoints work, refer to [this guide](/guides/base_endpoints_guide.md)

**Swagger Documentation**: 
  
  - [Base Endpoints](https://api.centerdenmark.com/swagger-ui/index.html#/)
  - [Transformation Endpoints](https://transform.centerdenmark.com/swagger-ui/index.html#/)

**Portal**: [User interface for API](https://portal.centerdenmark.com/)

An API for mapping between existing ids and data lake ids is being developed [here](https://transform.centerdenmark.com/swagger-ui/index.html). This api also allows posting complex json objects such that the user does not need to post individual entities to each table. 

---

## MQTT Reference (WIP)

*At time of writing, the MQTT broker is still being tested and is not yet available for public use.*

MQTT is only for timeseries data (PropertyValue).

The mqtt broker for the project can be connected to on the following address:

```bash
mqtt://mqtt.centerdenmark.com:8883
```

Authentication is required and uses token-based credentials integrated with the Center Denmark portal:
 * Username: The token ID
 * Password: The token itself

The token can be generated in the [portal](https://portal.centerdenmark.com/en-US/tokens). \
You can find a guide [here](https://portal.centerdenmark.com/en-US/help/token-management/token-creation).

The topic should ideally be:
```bash
{PropertyValueDatasetId}/{OrganisationId}/{BuildingId}/{BuildingSpaceId}/{DeviceId}/{PropertyId}
```

If this is not possible, please reach out to Center Denmark for assistance.