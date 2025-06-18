# Overview

This document provides a configuration guide for integrating with a third-party logistics API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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
  - `name`: "api_key"
  - `api_key`: Retrieved from `dlt.secrets["api_key"]`
  - `location`: "query"

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request
- **Data Selector**: "data"

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - Use neutral field names such as `id`, `customer_id`, etc.
- **Types and Nullability**: Define based on the API's response structure, ensuring consistency with the data model.

This configuration ensures a clean and efficient integration with the third-party logistics API, following dltHub's REST API source format and terminology.