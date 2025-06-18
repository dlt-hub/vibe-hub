# Overview

This document provides a configuration guide for integrating with a third-party task management API using dltHub's REST API source. The integration allows for data extraction from the API, supporting various synchronization types and configurations.

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
            "name": "task_resource",
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

## Resources

- **Resource Name**: `task_resource`
- **Endpoint Path**: `/v1/tasks`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration will retry requests on encountering HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2, up to a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `due_date`, and other task-related attributes.
- **Types**: Ensure all fields are defined with appropriate data types and nullability.
- **Neutral Field Names**: Use generic field names like `task_id` instead of any branded identifiers.

This configuration provides a clean, vendor-neutral setup for integrating with a task management API using dltHub's REST API source. Ensure all parameters are sourced from the provided YAML, and no proprietary or branded content is included.