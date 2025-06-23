# Overview

This document provides a configuration guide for integrating with a third-party task management API using dltHub's REST API source format. The integration supports data synchronization for task-related resources, utilizing a bearer token for authentication and cursor-based pagination for data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
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
                "path": "/users/@me/lists",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 5,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "lists_tasks",
            "endpoint": {
                "path": "/lists/{tasklist_id}/tasks",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 5,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Tasks

- **Endpoint Path**: `/users/@me/lists`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `pageToken`
  - **Cursor Path**: `nextPageToken`
- **Data Selector**: `items`
- **Primary Key**: `id`

### Lists Tasks

- **Endpoint Path**: `/lists/{tasklist_id}/tasks`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `pageToken`
  - **Cursor Path**: `nextPageToken`
- **Data Selector**: `items`
- **Primary Key**: `id`

## Error Handling

- **Retry Logic**: 
  - **Retry on Status Codes**: [429]
  - **Max Retries**: 5
  - **Backoff Factor**: 2

## Schema

### Tasks

- **Fields**:
  - `id`: string
  - `etag`: string or null
  - `kind`: string or null
  - `title`: string or null
  - `updated`: string
  - `selfLink`: string or null

### Lists Tasks

- **Fields**:
  - `id`: string
  - `due`: string or null
  - `etag`: string or null
  - `kind`: string or null
  - `links`: array or null
  - `title`: string or null
  - `status`: string or null
  - `updated`: string
  - `position`: string or null
  - `selfLink`: string or null
  - `completed`: string or null
  - `webViewLink`: string or null

This configuration provides a clean and vendor-neutral setup for integrating with a task management API, ensuring seamless data synchronization and error handling.