# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source format. The configuration is designed to facilitate data extraction from the API, supporting various synchronization types as specified.

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
- **Secrets or Config Values**: Use neutral keys such as `api_key` stored securely in `dlt.secrets`.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource`, default method is GET
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429, 500, 502, 503, and 504.
- **Fallback/Handling Behavior**: Define fallback mechanisms for handling API rate limits and server errors.

## Schema

- **Fields**: Use neutral field names such as `analytics_id` instead of any branded identifiers.
- **Types and Nullability**: Ensure consistency with the API's response schema, maintaining neutrality in field naming.

This configuration ensures a clean and generalized setup for integrating with a third-party analytics API, adhering to dltHub's REST API source format and avoiding any proprietary or branded content.