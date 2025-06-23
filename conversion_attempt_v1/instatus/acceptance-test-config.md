# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source. The integration supports full refresh sync types, allowing for the retrieval of data from the API in a structured and efficient manner.

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
  - Ensure consistency with the API's response structure

This configuration provides a clean and generalized setup for accessing a third-party analytics API, abstracting away any proprietary or branded content to ensure compatibility with dltHub's REST API source format.