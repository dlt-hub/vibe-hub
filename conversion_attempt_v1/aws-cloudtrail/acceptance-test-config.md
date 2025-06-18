# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source. The integration supports full refresh sync types, allowing for comprehensive data retrieval from the API.

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
- **Secrets or Config Values**: Use `api_key` stored securely in `dlt.secrets`.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource` (default method: GET)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle HTTP status codes such as 429 (Too Many Requests) and 500-series server errors with retries.
- **Fallback/Handling Behavior**: Implement fallback mechanisms for consistent data retrieval.

## Schema

- **Fields**: Use neutral field names such as `analytics_id` for identifiers.
- **Types and Nullability**: Ensure data types and nullability are consistent with the API's schema, using generalized field names.

This configuration provides a clean, reusable pattern for integrating with a third-party analytics API using dltHub's REST API source, abstracting away any proprietary or branded content.