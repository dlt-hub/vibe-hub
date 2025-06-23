# Overview

This document provides a vendor-neutral configuration for integrating with a third-party task management API using dltHub's REST API source format. The integration supports data synchronization through RESTful endpoints, allowing for efficient data retrieval and management.

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
- **Endpoint Path and Method**: `/v1/tasks`, default method is `GET`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request
- **Data Selector**: `tasks`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle HTTP status codes such as 429 (Too Many Requests) and 500-series server errors with retries.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and server downtime.

## Schema

- **Fields**: Use neutral field names such as `task_id`, `task_name`, `due_date`.
- **Types and Nullability**: Define field types and nullability based on the API's response schema, ensuring compatibility with dltHub's data ingestion requirements.

This configuration provides a clean and generalized approach to integrating with a task management API, ensuring compatibility with dltHub's REST API source format while abstracting away any proprietary or branded content.