# Overview

This document provides a configuration guide for integrating with a third-party weather data API. The API offers access to a network of automated weather stations, providing data to assist in efficient water resource management. The integration supports data retrieval for various resources, including weather data, station information, and spatial data.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "weather_data",
            "endpoint": {
                "path": "/v1/data",
                "data_selector": "Data.Providers.*.Records.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "Date",
                    "cursor_path": "Date"
                }
            }
        },
        {
            "name": "stations",
            "endpoint": {
                "path": "/v1/station",
                "data_selector": "Stations"
            }
        },
        {
            "name": "spatial_zipcodes",
            "endpoint": {
                "path": "/v1/spatialzipcode",
                "data_selector": "ZipCodes"
            }
        },
        {
            "name": "station_zipcodes",
            "endpoint": {
                "path": "/v1/stationzipcode",
                "data_selector": "ZipCodes"
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of each request.

## Resources

### Weather Data
- **Endpoint Path**: `/v1/data`
- **Primary Key**: `Julian`
- **Data Selector**: `Data.Providers.*.Records.*`
- **Pagination**: Cursor-based using the `Date` field

### Stations
- **Endpoint Path**: `/v1/station`
- **Primary Key**: `StationNbr`
- **Data Selector**: `Stations`

### Spatial Zipcodes
- **Endpoint Path**: `/v1/spatialzipcode`
- **Primary Key**: `ZipCode`
- **Data Selector**: `ZipCodes`

### Station Zipcodes
- **Endpoint Path**: `/v1/stationzipcode`
- **Primary Key**: Combination of `StationNbr` and `ZipCode`
- **Data Selector**: `ZipCodes`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Weather Data Schema
- **Fields**: Includes `Date`, `DayAirTmpAvg`, `DayAirTmpMax`, `DayAirTmpMin`, `DayAsceEto`, `DayDewPnt`, `DayPrecip`, `DayRelHumAvg`, `DayRelHumMax`, `DayRelHumMin`, `DaySoilTmpAvg`, `DaySolRadAvg`, `DayVapPresAvg`, `DayWindRun`, `DayWindSpdAvg`, `Julian`, `Scope`, `Standard`, `Station`, `ZipCodes`
- **Types**: String, object, or null

### Stations Schema
- **Fields**: Includes `City`, `ConnectDate`, `County`, `DisconnectDate`, `Elevation`, `GroundCover`, `HmsLatitude`, `HmsLongitude`, `IsActive`, `IsEtoStation`, `Name`, `RegionalOffice`, `SitingDesc`, `StationNbr`, `ZipCodes`
- **Types**: String or null

### Spatial Zipcodes Schema
- **Fields**: Includes `ConnectDate`, `DisconnectDate`, `IsActive`, `ZipCode`
- **Types**: String or null

### Station Zipcodes Schema
- **Fields**: Includes `ConnectDate`, `DisconnectDate`, `IsActive`, `StationNbr`, `ZipCode`
- **Types**: String or null

This configuration guide provides a clean, vendor-neutral setup for integrating with a third-party weather data API using dltHub's REST API source format.