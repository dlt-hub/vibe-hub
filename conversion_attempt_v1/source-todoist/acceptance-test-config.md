# Overview

This document provides a vendor-neutral configuration for integrating with a third-party task management API using dltHub's REST API source format. The integration supports full data synchronization from the API, with a focus on extracting task-related data.

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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

## Resources

- **Resource Name**: `tasks`
- **Endpoint Path**: `/v1/tasks`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `tasks` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `task_id`, `task_name`, `due_date`, and `completed`.
- **Types**: Each field is assigned a type consistent with the data returned by the API.
- **Nullability**: Fields are marked as nullable based on the API's response structure.

This configuration provides a clean and generalized setup for integrating with a task management API, ensuring compatibility with dltHub's REST API source format while abstracting away any proprietary or branded content.