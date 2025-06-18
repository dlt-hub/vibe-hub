# Overview

This document provides a configuration guide for integrating with a third-party weather API using dltHub's REST API source format. The integration allows for retrieving weather data, including current conditions, forecasts, and alerts, based on specified geographic coordinates. The API supports various units of measurement and languages for the response data.

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
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [500],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: The API key required for authentication, stored securely in dlt's secrets.

## Resources

- **Resource Name**: `weather_data`
- **Endpoint Path and Method**: 
  - Path: `/v1/resource`
  - Method: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: 
  - Retries on HTTP status code `500`
  - Maximum of 3 retries with a backoff factor of 2

## Schema

- **Fields**:
  - `alerts`: Array, Weather alerts for the requested location
  - `current`: Object, Contains current weather data
    - `clouds`: Number, Cloudiness percentage
    - `dew_point`: Number, Dew point temperature
    - `dt`: Number, Time of the data forecasted
    - `feels_like`: Number, Apparent (feels like) temperature
    - `humidity`: Number, Humidity percentage
    - `pressure`: Number, Atmospheric pressure on the sea level
    - `rain`: Object, Information about precipitation in the last hour
      - `1h`: Number, Rain volume for the last hour
    - `sunrise`: Number, Sunrise time
    - `sunset`: Number, Sunset time
    - `temp`: Number, Temperature
    - `uvi`: Number, UV index
    - `visibility`: Number, Visibility distance
    - `weather`: Array, Weather condition
    - `wind_deg`: Number, Wind direction in degrees
    - `wind_speed`: Number, Wind speed in meters per second
  - `daily`: Array, Weather forecast for the next 7 days
  - `hourly`: Array, Weather forecast for the next 48 hours
  - `lat`: Number, Latitude of the requested location
  - `lon`: Number, Longitude of the requested location
  - `minutely`: Array, Minute forecast for the next hour
  - `timezone`: String, Time zone name for the requested location
  - `timezone_offset`: Number, Time offset in seconds from UTC

This configuration provides a comprehensive setup for accessing weather data through a REST API, ensuring robust error handling and flexible data retrieval options.