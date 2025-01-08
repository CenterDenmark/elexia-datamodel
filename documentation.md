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

---

## How to Read the Data Model

- **Entities**: Each entity (e.g., `Building`, `Device`, `Observation`) represents a distinct type of object with specific attributes.
- **Relationships**: Relationships are stored in the `Relations` table.
- **Timestamps**: All timestamps follow ISO8601 format.
- **Attributes**: Descriptions provided below.

---

## Clarifications for Properties and Units of Measure

### Property

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

- **Attributes**:
  - `id`: Unique identifier.
  - `type`: Always "Building".
  - `name`: Building name.
  - `description`: Metadata.
  - `livingArea`: Area in square meters.
  - `businessArea`: Area in square meters.
  - `source`: Data source.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

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

### BuildingSpace

- **Attributes**:
  - `id`: Unique identifier.
  - `type`: Always "BuildingSpace".
  - `name`: Space name.
  - `alternateName`: Alternative name.
  - `area`: Area in square meters.
  - `airVolume`: Volume in cubic meters.
  - `buildingSpaceKind`: Type of space.
  - `source`: Data source.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

- **Example**:
```json
{
  "id": "Space001",
  "type": "BuildingSpace",
  "name": "Conference Room",
  "area": 50.0,
  "airVolume": 150.0,
  "buildingSpaceKind": "Room",
  "dateCreated": "2024-01-01T00:00:00Z",
  "dateModified": "2024-01-01T00:00:00Z"
}
```

### Device

- **Attributes**:
  - `id`: Unique identifier.
  - `type`: Device type (e.g., "Meter").
  - `name`: Device name.
  - `description`: Metadata.
  - `dateInstalled`: Installation date.
  - `status`: Device status.
  - `source`: Data source.
  - `dateCreated`: Creation timestamp.
  - `dateModified`: Last modification timestamp.

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

