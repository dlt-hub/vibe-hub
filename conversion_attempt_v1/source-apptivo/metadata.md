# Overview

This document provides a configuration guide for integrating with a third-party API using dltHub's REST API source. The integration allows for data extraction from a specified API endpoint, supporting various authentication methods and pagination strategies.

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
            "name": "resource_name",
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
  - `name`: The name of the API key parameter.
  - `api_key`: The API key value stored in dlt secrets.
  - `location`: Specifies where the API key is included in the request, such as in the query parameters.

## Resources

- **Resource Name**: A generic name for the resource being accessed.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: Default is GET.
- **Pagination Strategy**: 
  - **Type**: `offset`
  - **Limit**: 100
- **Data Selector**: Specifies the JSON path to the data within the API response.

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: Use neutral field names such as `customer_id` or `user_key`.
- **Types and Nullability**: Define the data types and nullability based on the API response structure.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party API using dltHub's REST API source, ensuring compatibility and ease of use.