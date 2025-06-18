# Overview

This document provides a vendor-neutral configuration for integrating with a third-party REST API using dltHub. The configuration is designed to facilitate data extraction from the API, supporting both full refresh and incremental sync types.

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
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely manage your API key.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource` (default method: GET)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests) and 500 (Internal Server Error) with retries.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and server errors gracefully.

## Schema

- **Fields**: Use neutral field names such as `id`, `name`, `created_at`, etc.
- **Types and Nullability**: Define field types and nullability based on the API's response schema, ensuring consistency and accuracy in data representation.

This configuration provides a clean and generalized approach to integrating with a REST API, abstracting away any proprietary or branded content to ensure compatibility with dltHub's open-source framework.