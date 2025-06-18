# Overview

This document provides a configuration example for integrating with a third-party API using dltHub's REST API source. The integration supports data synchronization from a generic API endpoint, abstracting away any vendor-specific details.

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

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504 with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.

## Schema

- **Fields**: Use neutral field names such as `id`, `data`, etc.
- **Types and Nullability**: Ensure consistency with the API's response schema, using generic field types and handling nullability as per the API's specifications.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party API using dltHub's REST API source, ensuring a seamless and efficient data synchronization process.