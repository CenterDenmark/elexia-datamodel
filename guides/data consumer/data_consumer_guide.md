# Center Denmark API Data Consumer Guide

## Overview

This guide describes the recommended workflow for retrieving data as a consumer using the Center Denmark API transformation endpoints. The workflow is based on the latest API documentation and focuses on efficient discovery and retrieval of entities and their relationships.

---

## Table of Contents

1. [Getting Started](#getting-started)
2. [Site Endpoints](#site-endpoints)
3. [Exploring Related Entities](#exploring-related-entities)
4. [Device Details](#device-details)
5. [Querying Time Series Data](#querying-time-series-data)
6. [Getting Latest Value Before Timestamp](#getting-latest-value-before-timestamp)
7. [Best Practices](#best-practices)
8. [API Reference](#api-reference)

---

## Getting Started

The main entry points for data consumers are the site endpoints:

- `/api/elexia/transformation/site/{siteId}`  
  Returns the site and all entities directly related to it (buildings, devices, weather areas, etc.).

- `/api/elexia/transformation/site/{siteId}/complete`  
  Returns the site and all entities recursively related to it, down to the device level (buildings, building spaces, devices, weather areas, forecasts, states, etc.).

Start by querying one of these endpoints with the UUID of the site you are interested in.

---

## Site Endpoints

### Get Site by ID

**Endpoint:**  
`GET /api/elexia/transformation/site/{siteId}`

**Description:**  
Returns the site and all entities directly related to it. This includes buildings, devices, weather areas, forecasts, and states that are directly linked to the site.

**Example from [data provider guide](/guides/data%20provider/transformation_endpoints_guide.md):**
```json
{
  "site": {
    "id": "40992cd5-721b-4d5f-bf85-19ae3170dd8e",
    "type": "site",
    "name": "Demo Site v3",
    "description": "Demo site for v3"
  },
  "deviceIds": [
    "0ac56c89-53fd-4d8c-b86a-59c20d9e4820"
  ],
  "buildingIds": [
    "445db277-11c5-4eb9-bdcb-82ef27d240d7"
  ],
  "weatherAreaIds": [
    "b75d68c2-3084-4fed-b784-ef68ee5fb3cc"
  ],
  "forecastModelIds": []
}
```

### Get Complete Site by ID

**Endpoint:**  
`GET /api/elexia/transformation/site/{siteId}/complete`

**Description:**  
Returns the site and all entities recursively related to it, including all buildings, building spaces, devices, weather areas, forecasts, and states under the site.

**Example from [data provider guide](/guides/data%20provider/transformation_endpoints_guide.md):**
```json
{
  "site": {
    "id": "40992cd5-721b-4d5f-bf85-19ae3170dd8e",
    "type": "site",
    "name": "Demo Site v3",
    "description": "Demo site for v3"
  },
  "devices": [
    {
      "device": {
        "id": "0ac56c89-53fd-4d8c-b86a-59c20d9e4820",
        "name": "Air Quality Sensor v3",
        "description": "Standalone sensor for CO₂ and humidity (not tied to a room)",
        "status": "Active"
      },
      "deviceSubType": {
        "type": "SENSOR",
        "dateInstalled": "2024-01-12T00:00:00Z"
      }
    }
  ],
  "buildings": [
    {
      "building": {
        "id": "445db277-11c5-4eb9-bdcb-82ef27d240d7",
        "type": "building",
        "name": "Demo Building v3",
        "description": "Demo building for v3",
        "livingArea": 0,
        "businessArea": 0
      },
      "buildingSpaces": [
        {
          "buildingSpace": {
            "id": "de5e8476-e762-4dcc-afc2-0c784d15f665",
            "type": "buildingSpace",
            "name": "Shared Wall Server-Office v3",
            "buildingSpaceKind": "Wall",
            "airVolume": 0,
            "area": 0
          },
          "state": [
            {
              "id": "85a22e5a-d446-4f35-99e6-5a2b45ccd84b",
              "name": "Wall Thermal Resistance v3",
              "type": "thermal_property",
              "description": "Thermal resistance property of the shared wall v3",
              "string_state": 0
            }
          ],
          "relatedBuildingSpaceIds": [
            "846b8f68-cb9d-43a1-9555-7bbfbe272ed9",
            "b1c3f9e8-a240-45fe-93c4-952aa6d08f2e"
          ]
        },
        {
          "buildingSpace": {
            "id": "846b8f68-cb9d-43a1-9555-7bbfbe272ed9",
            "type": "buildingSpace",
            "name": "Office Room v3",
            "buildingSpaceKind": "Room",
            "airVolume": 0,
            "area": 0
          },
          "devices": [
            {
              "device": {
                "id": "b08e2aeb-a67e-409d-9061-960fd9d1d2e1",
                "name": "Main Power Meter v3",
                "description": "Electricity meter in Office Room v3",
                "status": "Active"
              },
              "deviceSubType": {
                "type": "METER",
                "dateInstalled": "2024-01-15T00:00:00Z"
              }
            }
          ],
          "relatedBuildingSpaceIds": [
            "67dcdaa8-2283-43ea-9d36-9660cf3108a3",
            "de5e8476-e762-4dcc-afc2-0c784d15f665"
          ]
        },
        {
          "buildingSpace": {
            "id": "b1c3f9e8-a240-45fe-93c4-952aa6d08f2e",
            "type": "buildingSpace",
            "name": "Server Room v3",
            "buildingSpaceKind": "Room",
            "airVolume": 0,
            "area": 0
          },
          "devices": [
            {
              "device": {
                "id": "d4b4d8e8-f4f8-4493-ba43-fd5b1b183df8",
                "name": "Temp Sensor SR v3",
                "description": "Temperature sensor in Server Room v3",
                "status": "Active"
              },
              "deviceSubType": {
                "type": "SENSOR",
                "dateInstalled": "2024-01-10T00:00:00Z"
              }
            }
          ],
          "relatedBuildingSpaceIds": [
            "67dcdaa8-2283-43ea-9d36-9660cf3108a3",
            "de5e8476-e762-4dcc-afc2-0c784d15f665"
          ]
        },
        {
          "buildingSpace": {
            "id": "67dcdaa8-2283-43ea-9d36-9660cf3108a3",
            "type": "BuildingSpace",
            "name": "Shared Wall v3",
            "buildingSpaceKind": "Wall",
            "airVolume": 0,
            "area": 0
          },
          "relatedBuildingSpaceIds": [
            "846b8f68-cb9d-43a1-9555-7bbfbe272ed9",
            "b1c3f9e8-a240-45fe-93c4-952aa6d08f2e"
          ]
        }
      ]
    }
  ],
  "weatherAreas": [
    {
      "weatherArea": {
        "id": "b75d68c2-3084-4fed-b784-ef68ee5fb3cc",
        "name": "Rooftop Weather Area v3",
        "description": "Weather station area on the rooftop v3"
      },
      "devices": [
        {
          "device": {
            "id": "dca0cca7-54d4-45a5-975c-496ebcd25719",
            "name": "Weather Station v3",
            "description": "Weather monitoring station on rooftop v3",
            "status": "Active"
          },
          "deviceSubType": {
            "type": "SENSOR",
            "dateInstalled": "2024-01-20T00:00:00Z"
          }
        }
      ]
    }
  ],
  "forecastModels": [],
  "states": []
}
```

---

## Exploring Related Entities

The response from the site endpoints includes arrays of IDs for related entities. To explore these entities in detail, use the corresponding endpoints:

- **Building:**  
  `GET /api/elexia/transformation/building/{buildingId}`  
  Returns building details and directly related entities.

- **Weather Area:**  
  `GET /api/elexia/transformation/weatherarea/{weatherAreaId}`  
  Returns weather area details and directly related entities.

- **Device:**  
  `GET /api/elexia/transformation/device/{deviceId}`  
  Returns device details.  
  *Note: Currently, this endpoint does not return full observation details for the device. This will be updated in the future.*

  In addition to looking up observations for a device, the device endpoint should also be used to look up **states** and **forecasts** associated with the device. These are not included in the complete site endpoint and must be retrieved via the device endpoint:

  - **States for device:**  
    `GET /api/elexia/transformation/device/{deviceId}/states`
  - **Forecasts for device:**  
    `GET /api/elexia/transformation/device/{deviceId}/forecasts`

- **Forecast Model:**  
  `GET /api/elexia/transformation/forecast/{forecastId}`  
  Returns forecast model details.

- **State:**  
  `GET /api/elexia/transformation/state/by-id` (POST with array of state IDs)  
  Returns state details.

For recursive exploration, use the `/complete` endpoints where available (e.g., `/site/{siteId}/complete`).

---

## Device Details

To understand what a device measures, use:

- `GET /api/elexia/transformation/device/{deviceId}`

This endpoint returns metadata about the device, including its type, name, and description.  
**Note:** The current version does not return full observation details for the device. This will be improved in future API updates.

---

## Querying Time Series Data

To retrieve time series data (measurements, observations, forecasts, states), use the following endpoints:

- **Standard query endpoints:**  
  `/api/elexia/transformation/device/data/query`  
  `/api/elexia/transformation/forecast/data/query`  
  `/api/elexia/transformation/state/data/query`  
  These endpoints allow you to query data by specifying device, forecast, or state IDs, property IDs, and time ranges.

- **Optimized query endpoints (recommended):**  
  `/api/elexia/transformation/device/data/query/id`  
  `/api/elexia/transformation/forecast/data/query/id`  
  `/api/elexia/transformation/state/data/query/id`  
  These endpoints allow you to query data directly by observation IDs (for devices), forecast observation IDs (for forecasts), or state IDs (for states).  
  **Recommendation:** Use the `/query/id` endpoints for best performance, especially when you already know the relevant IDs.

**Example: Query device data by observation IDs**
```json
POST /api/elexia/transformation/device/data/query/id
{
  "observationIds": ["obs-uuid-202", "obs-uuid-303"],
  "startTime": "2024-01-01T00:00:00Z",
  "endTime": "2024-01-01T23:59:59Z"
}
```

**Example: Query forecast data by forecast observation IDs**
```json
POST /api/elexia/transformation/forecast/data/query/id
{
  "observationIds": ["forecast-obs-uuid-606"],
  "startTime": "2024-01-02T00:00:00Z",
  "endTime": "2024-01-02T23:59:59Z"
}
```

**Example: Query state data by state IDs**
```json
POST /api/elexia/transformation/state/data/query/id
{
  "stateIds": ["state-uuid-101", "state-uuid-102"],
  "startTime": "2024-01-01T00:00:00Z",
  "endTime": "2024-01-01T23:59:59Z"
}
```

---

## Getting Latest Value Before Timestamp

For all entities from which time series data can be collected (devices, forecasts, states), there is a dedicated endpoint to get the latest value before a given timestamp. This is useful for retrieving the most recent measurement or setpoint at a specific time.

- **Device observation:**  
  `/api/elexia/transformation/device/data/{observationId}/{timestamp}`

- **Forecast observation:**  
  `/api/elexia/transformation/forecast/data/query/{forecastObservationId}/{timestamp}`

- **State value:**  
  `/api/elexia/transformation/state/data/query/{stateId}/{timestamp}`

**Example: Get latest device value before timestamp**
```
GET /api/elexia/transformation/device/data/{observationId}/{timestamp}
```

**Example: Get latest forecast value before timestamp**
```
GET /api/elexia/transformation/forecast/data/query/{forecastObservationId}/{timestamp}
```

**Example: Get latest state value before timestamp**
```
GET /api/elexia/transformation/state/data/query/{stateId}/{timestamp}
```

---

## Best Practices

- **Start with the site endpoints** to discover the structure and related entities.
- **Use the complete endpoint** for a full recursive view of all entities under a site.
- **Follow up with entity-specific endpoints** to get details for buildings, devices, weather areas, forecasts, and states.
- **Cache context data** locally, as it changes infrequently.
- **Use UUIDs** from responses to query further details.
- **Monitor API updates** for improved device observation details.

---

## API Reference

Refer to the [Swagger documentation](https://transform.centerdenmark.com/swagger-ui/index.html#/) for full details on all endpoints, request/response formats, and available parameters.

- **Site endpoints:** `/api/elexia/transformation/site/{siteId}`, `/api/elexia/transformation/site/{siteId}/complete`
- **Device endpoint:** `/api/elexia/transformation/device/{deviceId}`, `/api/elexia/transformation/device/{deviceId}/states`, `/api/elexia/transformation/device/{deviceId}/forecasts`
- **Building endpoint:** `/api/elexia/transformation/building/{buildingId}`
- **Weather area endpoint:** `/api/elexia/transformation/weatherarea/{weatherAreaId}`
- **Forecast endpoint:** `/api/elexia/transformation/forecast/{forecastId}`
- **State endpoint:** `/api/elexia/transformation/state/by-id`
- **Time series data endpoints:**  
  `/api/elexia/transformation/device/data/query`, `/api/elexia/transformation/device/data/query/id`  
  `/api/elexia/transformation/forecast/data/query`, `/api/elexia/transformation/forecast/data/query/id`  
  `/api/elexia/transformation/state/data/query`, `/api/elexia/transformation/state/data/query/id`  
  `/api/elexia/transformation/device/data/{observationId}/{timestamp}`  
  `/api/elexia/transformation/forecast/data/query/{forecastObservationId}/{timestamp}`  
  `/api/elexia/transformation/state/data/query/{stateId}/{timestamp}`

For more information on the data model, see [data_model.md](/data_model.md).
