# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various resources, each with its own endpoint and data retrieval strategy. The API allows for incremental synchronization based on timestamps and supports pagination through cursor-based methods.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "employee",
            "endpoint": {
                "path": "/v1/employee",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "response.get('next') is None"
                }
            }
        },
        {
            "name": "team",
            "endpoint": {
                "path": "/v1/team",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "response.get('next') is None"
                }
            }
        },
        {
            "name": "shared_settings",
            "endpoint": {
                "path": "/v1/shared-settings",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "response.get('next') is None"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/project",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "response.get('next') is None"
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/app-tag",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "response.get('next') is None"
                }
            }
        },
        {
            "name": "app_category",
            "endpoint": {
                "path": "/v1/app-category",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "response.get('next') is None"
                }
            }
        },
        {
            "name": "directory",
            "endpoint": {
                "path": "/v1/directory",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "response.get('next') is None"
                }
            }
        },
        {
            "name": "scheduled_shift_settings",
            "endpoint": {
                "path": "/v1/scheduled-shift-settings",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "response.get('next') is None"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Use the `api_token` stored in dlt's secrets for authentication.

## Resources

### Employee
- **Endpoint Path**: `/v1/employee`
- **Pagination**: Cursor-based with `next` as the cursor parameter.
- **Data Selector**: `data`

### Team
- **Endpoint Path**: `/v1/team`
- **Pagination**: Cursor-based with `next` as the cursor parameter.
- **Data Selector**: `data`

### Shared Settings
- **Endpoint Path**: `/v1/shared-settings`
- **Pagination**: Cursor-based with `next` as the cursor parameter.
- **Data Selector**: `data`

### Projects
- **Endpoint Path**: `/v1/project`
- **Pagination**: Cursor-based with `next` as the cursor parameter.
- **Data Selector**: `data`

### Tags
- **Endpoint Path**: `/v1/app-tag`
- **Pagination**: Cursor-based with `next` as the cursor parameter.
- **Data Selector**: `data`

### App Category
- **Endpoint Path**: `/v1/app-category`
- **Pagination**: Cursor-based with `next` as the cursor parameter.
- **Data Selector**: `data`

### Directory
- **Endpoint Path**: `/v1/directory`
- **Pagination**: Cursor-based with `next` as the cursor parameter.
- **Data Selector**: `data`

### Scheduled Shift Settings
- **Endpoint Path**: `/v1/scheduled-shift-settings`
- **Pagination**: Cursor-based with `next` as the cursor parameter.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a schema defined with fields such as `id`, `name`, `createdAt`, and `updatedAt`. These fields are used to ensure data integrity and support incremental synchronization. The schemas are designed to be flexible, allowing for additional properties as needed.