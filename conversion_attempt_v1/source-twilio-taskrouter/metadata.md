# Overview

This document provides a vendor-neutral configuration for integrating with a third-party task management API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data retrieval and management.

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
            "name": "task_management_resource",
            "endpoint": {
                "path": "/v1/tasks",
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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `task_management_resource`
- **Endpoint Path**: `/v1/tasks`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied for retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `status`, and other task-related attributes.
- **Types**: Fields are typed according to their data (e.g., string, integer).
- **Nullability**: Fields may be nullable depending on the API's response structure.

This configuration provides a clean and generalized setup for integrating with a task management API, ensuring compatibility with dltHub's REST API source framework.