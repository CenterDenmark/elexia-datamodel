# Data Model Documentation

## Table of Contents

1. [How to Read the Data Model](#how-to-read-the-data-model)
2. [Clarifications for Properties and Units of Measure](#clarifications-for-properties-and-units-of-measure)
   - [Property](#property)
   - [UnitOfMeasure](#unitofmeasure)
3. [Entities](#entities)
   - [Building](#building)
   - [BuildingSpace](#buildingspace)
   - [Device](#device)
   - [Observation](#observation)
   - [Address](#address)
   - [Site](#site)
   - [Location](#location)
   - [Organisation](#organisation)
   - [PropertyValue](#propertyvalue)
4. [Relations](#relations)
5. [API Reference](#api-reference)

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
  entityFrom: Building123, entity_classFrom: Building
  entityTo: Space456, entity_classTo: BuildingSpace
  type: hasSpace
  ```

- **Timestamps**: All timestamps follow the XSD datetime standard (ISO8601 format).

- **Attributes**: Each entity's attributes are described below, with references where applicable.

---

## Clarifications for Properties and Units of Measure

### Property

A `Property` represents a measurable or observable attribute, such as temperature, humidity, or energy consumption.

- **Definition**:
  - **QUDT Quantity Kinds**: Properties are based on the [QUDT Quantity Kinds vocabulary](https://www.qudt.org/doc/DOC_VOCAB-QUANTITY-KINDS.html#Instances).

* **Attributes**:

  - `id` (UUID): Unique identifier for the property.
  - `type`: Always `"Property"`.
  - `name`: A descriptive name (e.g., `"Temperature"`) aligned with QUDT terminology.
  - `description`: Additional context or details.
  - `dateCreated`, `dateModified`: Metadata fields.

* **Example**:

  ```
  id: Property001
  type: Property
  name: "Temperature"
  description: "Represents the thermal state of an object"
  ```

### UnitOfMeasure

Represents the unit used to measure a `Property`.

- **Definition**:
  - Units must be based on the [QUDT Units vocabulary](https://www.qudt.org/doc/DOC_VOCAB-UNITS.html).

* **Attributes**:

  - `id` (UUID): Unique identifier for the unit.
  - `name`: Corresponds to the URI name in the QUDT Units vocabulary, i.e. the name for ampere is "A".
  - `description`: Details about the unit.

* **Example**:

  ```
  id: Unit001
  name: "DEG_C"
  description: "Degrees Celsius, a measure of temperature"
  ```

---

## Entities

### Building

Represents a physical structure.

- **Attributes**:

  - `id` (UUID): Unique identifier.
  - `type`: Always `"Building"`.
  - `name`, `description`: General metadata.
  - `livingArea`, `businessArea`: Areas in square meters.
  - `source`, `dateCreated`, `dateModified`: Metadata fields.

- **Reference**:

  - Based on attributes from [Smart Data Models S4BLDG/Building](https://github.com/smart-data-models/dataModel.S4BLDG/blob/master/Building/schema.json).

- **Example**:

  ```
  id: Building123
  type: Building
  name: "Office Building A"
  livingArea: 1000.0
  businessArea: 500.0
  ```

---

### BuildingSpace

Defines spatial elements of a building and supports hierarchical relationships.

- **Attributes**:

  - `id` (UUID): Unique identifier.
  - `type`: Always `"BuildingSpace"`.
  - `name`, `alternateName`, `description`, `area`, `airVolume`.
  - `buildingSpaceKind`: Specifies the type (e.g., `"Room"`, `"Storey"`).
  - `source`, `dataProvider`, `dateCreated`, `dateModified`.

- **Reference**:

  - Based on attributes from [Smart Data Models S4BLDG/BuildingSpace](https://github.com/smart-data-models/dataModel.S4BLDG/blob/master/BuildingSpace/schema.json).

- **Example**:

  ```
  id: Space001
  type: BuildingSpace
  name: "Conference Room"
  area: 50.0
  airVolume: 150.0
  buildingSpaceKind: "Room"
  ```

---

### Device

Represents a physical device.

- **Attributes**:

  - `id` (UUID): Unique identifier.
  - `type`: Specifies the type (e.g., `"Meter"`).
  - `name`, `description`, `dateInstalled`, `status`.
  - `source`, `dateCreated`, `dateModified`.

- **Reference**:

  - Based on [SAREF Device](https://saref.etsi.org/core/v3.2.1/#saref\:Device).

- **Example**:

  ```
  id: Device123
  type: Meter
  name: "Electric Meter"
  description: "Measures electricity usage"
  ```

---

### Observation

Represents a measurable event where a `Device` measures a specific `Property`.

- **Attributes**:

  - `id` (UUID): Unique identifier.
  - `type`: Always `"Observation"`.
  - `description`, `temporalType` (e.g., `"Interval"`, `"Instant"`).
  - `dateCreated`, `dateModified`: Metadata.

- **Example**:

  ```
  id: Obs001
  type: Observation
  description: "Temperature measurement"
  temporalType: "Instant"
  ```

---

### Address

Defines a postal address associated with entities like `Building` or `Organisation`.

- **Definition**

  - The entity is defined based on [https://schema.org/address](https://schema.org/address).

- **Attributes**:

  - `id` (UUID): Unique identifier.
  - `type`: Always `"PostalAddress"`.
  - `streetAddress`, `streetNr`, `addressLocality`, `addressRegion`, `postalCode`, `addressCountry`, `postOfficeBoxNumber`.
  - `district`, `source`, `dateCreated`, `dateModified`.

- **Example**:

  ```
  id: Address001
  type: PostalAddress
  streetAddress: "123 Main St"
  streetNr: "456"
  addressLocality: "Springfield"
  addressRegion: "IL"
  postalCode: "62704"
  addressCountry: "USA"
  ```

---

### Site

Represents a geographic or administrative area associated with buildings or organisations.

- **Attributes**:

  - `id` (UUID): Unique identifier.
  - `type`: Always `"Site"`.
  - `name`, `description`, `source`, `dateCreated`, `dateModified`.

- **Example**:

  ```
  id: Site001
  type: Site
  name: "Downtown Area"
  description: "Central business district"
  ```

---

### Location

Defines a spatial location associated with other entities.

- **Attributes**:

  - `id` (UUID): Unique identifier.
  - `type`: Always `"Location"`.
  - `value`: A GeoJSON string representing the location.
  - `source`, `dateCreated`, `dateModified`.

- **Example**:

  ```
  id: Location001
  type: Location
  value: "{\"type\": \"Point\", \"coordinates\": [39.7817, -89.6501]}"
  ```

---

### Organisation

Represents an organisation that owns or manages entities such as `Building` or `Site`.

- **Attributes**:

  - `id` (UUID): Unique identifier.
  - `type`: Always `"Organisation"`.
  - `name`, `description`, `address`, `contactPoint`, `source`, `dateCreated`, `dateModified`.

- **Example**:

  ```
  id: Organisation001
  type: Organisation
  name: "Green Energy Co."
  description: "A company focused on renewable energy solutions."
  address: "Address001"
  contactPoint: "info@greenenergy.com"
  ```

---

### PropertyValue

Stores the results of an observation.

- **Attributes**:

  - `procedureExecution`: Links to the observation.
  - `value`: Measured value.
  - `timestamp`: When the value was recorded.
  - `source`, `dateCreated`, `dateModified`.

- **Example**:

  ```
  procedureExecution: Obs001
  value: 22.5
  timestamp: 2024-06-16T12:00:00Z
  ```

---

### Relations

Defines relationships between entities.

- **Columns**:

  - `entityFrom`, `entity_classFrom`: Source entity and its type.
  - `entityTo`, `entity_classTo`: Target entity and its type.
  - `type`: Type of relationship.

- **Example**:

  ```
  entityFrom: Device123
  entity_classFrom: Device
  entityTo: Obs001
  entity_classTo: Observation
  type: madeExecution
  ```

---

## API Reference

For interacting with the data model, the following API endpoints are used:

1. **Relational Data**:\
   POST data to:

   ```
   /api/v1/dataset/relational/{datasetId}
   ```

2. **Timeseries Data** (`PropertyValue` and `PropertyValueInterval`):\
   POST data to:

   ```
   /api/v1/dataset/timeseries/{datasetId}
   ```

**Swagger Documentation**: [API Documentation](https://api.centerdenmark.com/swagger-ui/index.html#/)

**Portal**: [User interface for API](https://portal.centerdenmark.com/)

---

