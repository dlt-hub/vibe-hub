# Overview

This document provides a vendor-neutral configuration for integrating with a third-party weather data API using dltHub's REST API source format. The integration supports fetching weather data through a RESTful API, allowing for data synchronization and analysis.

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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `weather_data`
- **Endpoint Path**: `/v1/weather`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration will retry requests on encountering HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts will be made before failing.
- **Backoff Factor**: A backoff factor of 2 is used to progressively delay retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `temperature`, `humidity`, and `conditions`.
- **Types**: Ensure that field types are consistent with the API's response format.
- **Nullability**: Fields may be nullable depending on the API's data availability.

This configuration provides a clean and generalized setup for accessing weather data from a third-party API, ensuring compatibility with dltHub's REST API source framework.