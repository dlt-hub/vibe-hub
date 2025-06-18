# Overview

This document provides a configuration guide for integrating with a third-party task management API using dltHub's REST API source format. The integration supports data synchronization for resources such as workspaces and workers, utilizing a cursor-based pagination strategy for efficient data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["account_sid"],
            "password": dlt.secrets["auth_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "sid",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "workspaces",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "workspaces",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "meta.next_page_url"
                }
            }
        },
        {
            "name": "workers",
            "endpoint": {
                "path": "/v1/resource/{{ stream_slice.id }}/Workers",
                "data_selector": "workers",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "meta.next_page_url"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**:
  - `account_sid`: The account identifier.
  - `auth_token`: The authentication token.

## Resources

### Workspaces

- **Resource Name**: workspaces
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based
  - **Cursor Path**: `meta.next_page_url`
  - **Limit**: 50
- **Data Selector**: `workspaces`

### Workers

- **Resource Name**: workers
- **Endpoint Path**: `/v1/resource/{{ stream_slice.id }}/Workers`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based
  - **Cursor Path**: `meta.next_page_url`
  - **Limit**: 50
- **Data Selector**: `workers`

## Error Handling

- **Retry Logic**:
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

### Workspaces

- **Fields**:
  - `account_sid`: The unique identifier for the account associated with the workspace.
  - `date_created`: The date and time when the workspace was created.
  - `date_updated`: The date and time when the workspace was last updated.
  - `default_activity_name`: The name of the default activity for the workspace.
  - `default_activity_sid`: The unique identifier for the default activity of the workspace.
  - `event_callback_url`: The URL to which task events will be sent.
  - `events_filter`: Filter for specifying which events should be sent to the callback URL.
  - `friendly_name`: A user-friendly name for the workspace.
  - `multi_task_enabled`: Indicates whether multitasking is enabled for the workspace.
  - `sid`: The unique identifier for the workspace.
  - `timeout_activity_sid`: The unique identifier for the activity that tasks are routed to on timeout.
  - `timeout_activity_name`: The name of the activity that tasks are routed to on timeout.
  - `url`: The URL of the workspace.

### Workers

- **Fields**:
  - `account_sid`: The unique identifier for the account that this worker belongs to.
  - `activity_sid`: The unique identifier of the current activity of the worker.
  - `activity_name`: The name of the current activity of the worker.
  - `attributes`: Custom attributes or metadata associated with the worker.
  - `available`: Indicates if the worker is available for tasks.
  - `date_created`: The date and time when the worker was created.
  - `date_status_changed`: The date and time when the worker's status was last changed.
  - `date_updated`: The date and time when the worker was last updated.
  - `friendly_name`: A friendly name or label for the worker.
  - `sid`: The unique identifier for the worker.
  - `url`: The resource URL for accessing details of the worker.
  - `workspace_sid`: The unique identifier for the workspace that this worker belongs to.