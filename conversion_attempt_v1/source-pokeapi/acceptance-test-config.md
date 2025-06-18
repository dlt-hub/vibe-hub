```markdown
# Overview

This document provides a configuration guide for integrating with a third-party REST API using dltHub's REST API source. The integration supports full data synchronization from the API, utilizing a straightforward configuration for authentication, resource management, and error handling.

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

The API requires an API key for authentication. The key should be included in the query parameters of each request.

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

## Resources

- **Resource Name**: A generic name for the data resource.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

The integration includes basic error handling with retry logic for common HTTP status codes.

- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema is defined based on the fields returned by the API. Use neutral field names to ensure compatibility and avoid brand-specific terminology.

- **Fields**: Include fields such as `id`, `name`, `value`, etc.
- **Types**: Define appropriate data types for each field.
- **Nullability**: Specify if fields can be null based on the API's response structure.

This configuration provides a clean and vendor-neutral setup for integrating with a third-party REST API using dltHub's REST API source. Follow the guidelines to ensure a seamless data synchronization process.
```