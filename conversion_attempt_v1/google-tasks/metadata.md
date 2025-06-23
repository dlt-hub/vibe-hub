# Overview

This document provides a vendor-neutral configuration for integrating with a third-party task management API using dltHub's REST API source format. The integration supports data synchronization from the API, allowing for efficient data extraction and processing.

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
        "primary_key": "task_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "tasks",
            "endpoint": {
                "path": "/v1/tasks",
                "data_selector": "tasks",
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

- **Resource Name**: `tasks`
- **Endpoint Path and Method**: `/v1/tasks`, default method is GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: `tasks`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.

## Schema

- **Fields**: Use neutral field names such as `task_id`, `task_name`, `due_date`, etc.
- **Types and Nullability**: Define fields with appropriate data types and nullability based on the API's response schema.

This configuration ensures a clean and efficient integration with the task management API, abstracting away any proprietary or branded content for a generalized and reusable setup.