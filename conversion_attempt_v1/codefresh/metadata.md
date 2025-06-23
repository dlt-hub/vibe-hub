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

- **Type**: API Key
- **Configuration**: 
  - `api_key`: Stored securely in `dlt.secrets["api_key"]`
  - **Location**: Query parameter

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset
  - **Limit**: 100
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**:
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - Use neutral field names such as `customer_id`, `transaction_id`, etc.
- **Types**: Consistent with the API's response structure
- **Nullability**: As defined by the API's schema

This configuration provides a clean, vendor-neutral setup for integrating with a third-party API using dltHub's REST API source, ensuring a seamless data extraction process.