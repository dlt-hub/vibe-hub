# Overview

This document provides a vendor-neutral configuration for integrating with a third-party API using dltHub's REST API source. The integration supports incremental synchronization and is designed to handle various API endpoints efficiently.

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

- **Resource Name**: Abstracted to a neutral name like `resource_name`.
- **Endpoint Path and Method**: `/v1/resource` with the default HTTP method as GET.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429, 500, 502, 503, and 504.
- **Fallback/Handling Behavior**: Generalized error handling to ensure robust data extraction.

## Schema

- **Fields**: Use neutral field names such as `id`, `name`, `created_at`, etc.
- **Types and Nullability**: Ensure consistency with the API's response schema, using generic types and handling null values appropriately.

This configuration is designed to be flexible and adaptable to various third-party APIs, providing a clean and efficient way to integrate and synchronize data using dltHub's REST API source.