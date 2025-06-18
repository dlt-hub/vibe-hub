# Overview

This document provides a configuration guide for integrating with a third-party weather API using dltHub's REST API source format. The integration allows for the retrieval of weather data through a RESTful API, supporting various data synchronization methods.

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
                "path": "/v1/weather",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: The API key should be stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `weather_data`
- **Endpoint Path**: `/v1/weather`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `temperature`, `humidity`, and `weather_condition`.
- **Types**: Each field is defined with a specific data type, such as integer or string.
- **Nullability**: Fields may be nullable depending on the API response.

This configuration provides a clean and vendor-neutral setup for accessing weather data through a REST API, ensuring compatibility with dltHub's data integration framework.