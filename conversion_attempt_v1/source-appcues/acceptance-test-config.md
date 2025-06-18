# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source. The configuration is designed to facilitate data extraction from the API, supporting both full refresh and incremental sync types.

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
- **Secrets or Config Values**: 
  - `api_key`: Stored securely in `dlt.secrets["api_key"]`
- **Location**: Query parameter

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: 
  - Path: `/v1/resource`
  - Method: `GET` (default)
- **Pagination Strategy**: 
  - Type: `offset`
  - Limit: `100`
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: 
  - Retry on status codes: `[429, 500, 502, 503, 504]`
  - Maximum retries: `3`
  - Backoff factor: `2`
- **Known HTTP Response Codes**: 
  - Handles common server errors and rate limiting

## Schema

- **Fields**: 
  - Use neutral field names such as `analytics_id`, `customer_id`, etc.
- **Types and Nullability**: 
  - Define based on the API's response structure, ensuring consistency with the data returned.

This configuration ensures a clean and efficient integration with the third-party analytics API, abstracting away any proprietary or branded content for a generalized and reusable setup.