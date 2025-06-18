# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API uses OAuth2 for authentication and supports incremental data synchronization based on timestamps.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/authorization/token",
            "refresh_token": dlt.secrets["refresh_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects.json",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "not headers.link.next.url"
                }
            }
        },
        {
            "name": "schedules",
            "endpoint": {
                "path": "/v1/projects/{project_id}.json",
                "data_selector": "dock.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "not headers.link.next.url"
                }
            }
        },
        {
            "name": "schedule_entries",
            "endpoint": {
                "path": "/v1/buckets/{project_id}/schedules/{schedule_id}/entries.json",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "not headers.link.next.url"
                }
            }
        },
        {
            "name": "todos",
            "endpoint": {
                "path": "/v1/projects/recordings.json",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "not headers.link.next.url"
                }
            },
            "incremental_sync": {
                "cursor_field": "updated_at",
                "start_datetime": dlt.config["start_date"]
            }
        },
        {
            "name": "messages",
            "endpoint": {
                "path": "/v1/projects/recordings.json",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "not headers.link.next.url"
                }
            },
            "incremental_sync": {
                "cursor_field": "updated_at",
                "start_datetime": dlt.config["start_date"]
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: `client_id` (stored in secrets)
- **Client Secret**: `client_secret` (stored in secrets)
- **Token URL**: `https://api.example.com/authorization/token`
- **Refresh Token**: `refresh_token` (stored in secrets)

# Resources

### Projects
- **Endpoint Path**: `/v1/projects.json`
- **Pagination**: Cursor-based
- **Data Selector**: `data`

### Schedules
- **Endpoint Path**: `/v1/projects/{project_id}.json`
- **Pagination**: Cursor-based
- **Data Selector**: `dock.*`

### Schedule Entries
- **Endpoint Path**: `/v1/buckets/{project_id}/schedules/{schedule_id}/entries.json`
- **Pagination**: Cursor-based
- **Data Selector**: `data`

### Todos
- **Endpoint Path**: `/v1/projects/recordings.json`
- **Pagination**: Cursor-based
- **Data Selector**: `data`
- **Incremental Sync**: Based on `updated_at`

### Messages
- **Endpoint Path**: `/v1/projects/recordings.json`
- **Pagination**: Cursor-based
- **Data Selector**: `data`
- **Incremental Sync**: Based on `updated_at`

# Error Handling

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has its own schema, with fields such as `id`, `name`, `created_at`, and `updated_at`. The schemas are designed to be flexible, allowing for additional properties as needed.