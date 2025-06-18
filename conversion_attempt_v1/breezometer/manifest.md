# Overview

This document provides a configuration guide for integrating with a third-party environmental data API using dltHub's REST API source format. The API provides access to various environmental data streams, including air quality, pollen forecasts, weather conditions, and wildfire information. The integration supports fetching current, forecasted, and historical data.

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
            "name": "air_quality_current",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "air_quality_forecast",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "air_quality_historical",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "pollen_forecast",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "weather_current",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "weather_forecast",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "wildfire_burnt_area",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "wildfire_locate",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

## Resources

### Air Quality Current
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Air Quality Forecast
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Air Quality Historical
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Pollen Forecast
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Weather Current
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Weather Forecast
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Wildfire Burnt Area
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Wildfire Locate
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

- **Fields**: Use neutral field names such as `datetime`, `data_available`, `indexes`, etc.
- **Types**: Include types like `string`, `boolean`, `integer`, `object`, `array`, and `number`.
- **Nullability**: Fields can be `null` or the specified type.