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
- **Secrets or Config Values**: 
  - `api_key`: Stored securely in `dlt.secrets["api_key"]`
  - **Location**: `query` (can also be `header` if required by the API)

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: 
  - **Path**: `/v1/resource`
  - **Method**: `GET` (default)
- **Pagination Strategy**: 
  - **Type**: `offset`
  - **Limit**: 100
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**:
  - **Retry on Status Codes**: [429, 500, 502, 503, 504]
  - **Max Retries**: 3
  - **Backoff Factor**: 2
- **Known HTTP Response Codes**: Handles common server errors and rate limiting
- **Fallback/Handling Behavior**: Implements exponential backoff for retries

## Schema

- **Fields**: 
  - `id`: Primary key
  - `data`: Main data field
- **Types and Nullability**: Ensure fields are consistent with the API's response structure
- **Neutral Field Names**: Use generic terms like `customer_id` instead of branded identifiers

This guide ensures a clean and efficient integration with a third-party analytics API, abstracting away any proprietary or branded content for a seamless experience with dltHub's REST API source.