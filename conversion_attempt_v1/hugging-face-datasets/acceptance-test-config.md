# Overview

This document provides a vendor-neutral configuration for integrating with a third-party datasets API using dltHub's REST API source. The integration supports data synchronization through a RESTful interface, allowing for efficient data retrieval and management.

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
  - **Location**: `query`

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

## Schema

- **Fields**: 
  - Use neutral field names such as `analytics_id` instead of any proprietary identifiers.
- **Types and Nullability**: 
  - Ensure consistency with the API's response schema, using generic field names and types.

This configuration provides a clean and generalized setup for integrating with a third-party datasets API, ensuring compatibility with dltHub's REST API source format.