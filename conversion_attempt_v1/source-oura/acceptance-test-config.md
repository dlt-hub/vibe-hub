# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization through a RESTful interface, allowing for efficient data extraction and management.

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
  - Handles common server errors and rate limiting
- **Fallback/Handling Behavior**: 
  - Implements exponential backoff for retries

## Schema

- **Fields**: 
  - Use neutral field names such as `customer_id`, `event_timestamp`, etc.
- **Types and Nullability**: 
  - Ensure consistency with the API's response structure
- **Example Fields**: 
  - `id`: Primary key
  - `data`: Main data payload

This guide ensures a clean and efficient integration with a third-party analytics API, abstracting away any proprietary or branded content for a generalized and reusable configuration.