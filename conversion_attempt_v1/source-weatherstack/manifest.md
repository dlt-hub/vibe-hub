# Overview

This document provides a configuration guide for integrating with a third-party weather API using the dltHub REST API source format. The integration allows for retrieving current weather, forecast, historical weather data, and location lookup information. The API supports various data retrieval methods, including current weather conditions, weather forecasts, historical weather data, and location-based queries.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "access_key",
            "api_key": dlt.secrets["access_key"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "current_weather",
            "endpoint": {
                "path": "/v1/current",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "forecast",
            "endpoint": {
                "path": "/v1/forecast",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "historical",
            "endpoint": {
                "path": "/v1/historical",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "location_lookup",
            "endpoint": {
                "path": "/v1/autocomplete",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**:
  - `api_key`: The API access key is stored securely in `dlt.secrets["access_key"]`.
  - **Location**: The API key is included in the query parameters of the request.

# Resources

## Current Weather
- **Resource Name**: `current_weather`
- **Endpoint Path**: `/v1/current`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

## Forecast
- **Resource Name**: `forecast`
- **Endpoint Path**: `/v1/forecast`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

## Historical
- **Resource Name**: `historical`
- **Endpoint Path**: `/v1/historical`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

## Location Lookup
- **Resource Name**: `location_lookup`
- **Endpoint Path**: `/v1/autocomplete`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**:
  - **Retry on Status Codes**: 429, 500, 502, 503, 504
  - **Max Retries**: 3
  - **Backoff Factor**: 2

# Schema

The schema for each resource is defined using a JSON schema format. The fields are generalized and include types such as `string`, `number`, `array`, and `object`. The schema is designed to be flexible and accommodate various data structures returned by the API.

- **Fields**:
  - `request`: Contains query parameters and request metadata.
  - `location`: Provides location-specific information.
  - `current`: Details current weather conditions.
  - `forecast`: Includes forecast data.
  - `historical`: Contains historical weather data.
  - `results`: Used in location lookup to return matching locations.

This configuration provides a comprehensive setup for integrating with a weather API using dltHub's REST API source format, ensuring a clean and vendor-neutral implementation.