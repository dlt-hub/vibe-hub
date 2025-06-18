# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization through a RESTful interface, allowing for efficient data extraction and transformation.

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
  - Limit: 100
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: 
  - Retry on status codes: [429, 500, 502, 503, 504]
  - Maximum retries: 3
  - Backoff factor: 2
- **Known HTTP Response Codes**: 
  - 429: Too Many Requests
  - 500: Internal Server Error
  - 502: Bad Gateway
  - 503: Service Unavailable
  - 504: Gateway Timeout

## Schema

- **Fields**: 
  - Use neutral field names such as `customer_id`, `event_type`, etc.
- **Types and Nullability**: 
  - Define fields based on the data structure returned by the API, ensuring consistency and neutrality.

This configuration provides a clean and generalized approach to integrating with a REST API, abstracting away any proprietary or branded content to ensure compatibility with dltHub's open-source framework.