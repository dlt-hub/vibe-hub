# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from the API, abstracting away any proprietary or branded content.

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
- **Secrets or Config Values**: Use neutral keys such as `api_key` for authentication.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource`, default method is GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100.
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429, 500, 502, 503, and 504.
- **Fallback/Handling Behavior**: Define fallback mechanisms for handling API rate limits and server errors.

## Schema

- **Fields**: Use neutral field names such as `analytics_id` instead of any branded identifiers.
- **Types and Nullability**: Ensure consistency with the API's data schema, using generic field types and nullability settings.

This configuration provides a clean, generalized approach to integrating with a third-party analytics API, ensuring compatibility with dltHub's REST API source format while abstracting away any proprietary details.