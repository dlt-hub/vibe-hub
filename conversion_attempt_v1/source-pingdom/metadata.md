# Overview

This document provides a configuration guide for integrating with a third-party monitoring API using dltHub's REST API source. The integration allows for data extraction from the API, supporting various sync types and configurations.

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
            "name": "monitoring_data",
            "endpoint": {
                "path": "/v1/resource",
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
- **Configuration**: 
  - `api_key` is stored securely using `dlt.secrets["api_key"]`.
  - The API key is included in the query parameters of each request.

## Resources

- **Resource Name**: `monitoring_data`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: 
  - Retries are configured for HTTP status codes 429, 500, 502, 503, and 504.
  - A maximum of 3 retries with an exponential backoff factor of 2 is implemented.

## Schema

- **Fields**: The schema includes fields such as `id`, `status`, `response_time`, etc.
- **Field Types**: Ensure all field types are consistent with the API's response.
- **Nullability**: Fields are nullable based on the API's data structure.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party monitoring API using dltHub's REST API source. All parameters and settings are derived directly from the provided YAML configuration, ensuring a seamless and efficient data extraction process.