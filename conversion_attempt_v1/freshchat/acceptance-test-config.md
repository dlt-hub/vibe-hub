# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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
  - **Location**: `query`

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: `GET` (default)
- **Pagination Strategy**: 
  - **Type**: `offset`
  - **Limit**: 100
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - **Retry on Status Codes**: [429, 500, 502, 503, 504]
  - **Max Retries**: 3
  - **Backoff Factor**: 2

## Schema

- **Fields**: 
  - `id`: Primary key
  - Use neutral field names for data consistency

This guide ensures a clean and efficient setup for integrating with a third-party analytics API using dltHub's REST API source, abstracting away any proprietary or branded content.