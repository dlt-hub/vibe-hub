# Overview

This document provides a vendor-neutral configuration for integrating with a third-party API using dltHub's REST API source. The configuration is designed to facilitate data extraction from a generic API endpoint, supporting both full refresh and incremental sync types.

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
- **Endpoint Path and Method**: `/v1/resource`, default method is `GET`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle HTTP status codes such as 429, 500, 502, 503, and 504 with retries.
- **Fallback/Handling Behavior**: Define fallback mechanisms for persistent errors or timeouts.

## Schema

- **Fields**: Use neutral field names such as `id`, `name`, `created_at`, etc.
- **Types and Nullability**: Define field types and nullability based on the API's response schema, ensuring consistency and data integrity.

This configuration provides a clean and generalized approach to integrating with a third-party API, abstracting away any proprietary or branded content. It ensures compatibility with dltHub's REST API source format, allowing for seamless data extraction and processing.