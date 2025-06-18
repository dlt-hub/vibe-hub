# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data formats and configurations, ensuring flexibility and adaptability to different data structures and requirements.

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
  - `api_key`: Stored securely in dlt secrets
  - **Location**: `query` (can also be `header` if required)

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: 
  - **Path**: `/v1/resource`
  - **Method**: `GET` (default)
- **Pagination Strategy**: 
  - **Type**: `offset`
  - **Limit**: `100`
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**:
  - **Retry on Status Codes**: `[429, 500, 502, 503, 504]`
  - **Max Retries**: `3`
  - **Backoff Factor**: `2`
- **Known HTTP Response Codes**: Handles common server errors and rate limiting

## Schema

- **Fields**: Use neutral field names such as `customer_id`, `event_type`, etc.
- **Types and Nullability**: Ensure compatibility with various data types and handle null values appropriately.

This configuration ensures a clean and efficient integration with a third-party analytics API, abstracting away any vendor-specific details and focusing on the essential parameters required for a successful data sync.