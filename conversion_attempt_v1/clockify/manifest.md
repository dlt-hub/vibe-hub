# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as users, projects, clients, tags, user groups, time entries, and tasks. Each resource is accessed via a specific endpoint and supports pagination and authentication.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/workspaces/{workspace_id}/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page-size",
                    "limit": 50
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/workspaces/{workspace_id}/projects",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page-size",
                    "limit": 50
                }
            }
        },
        {
            "name": "clients",
            "endpoint": {
                "path": "/v1/workspaces/{workspace_id}/clients",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page-size",
                    "limit": 50
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/workspaces/{workspace_id}/tags",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page-size",
                    "limit": 50
                }
            }
        },
        {
            "name": "user_groups",
            "endpoint": {
                "path": "/v1/workspaces/{workspace_id}/user-groups",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page-size",
                    "limit": 50
                }
            }
        },
        {
            "name": "time_entries",
            "endpoint": {
                "path": "/v1/workspaces/{workspace_id}/user/{user_id}/time-entries",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page-size",
                    "limit": 50
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/v1/workspaces/{workspace_id}/projects/{project_id}/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page-size",
                    "limit": 50
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`.
- **Location**: Header with the field name `X-Api-Key`.

## Resources

### Users
- **Endpoint Path**: `/v1/workspaces/{workspace_id}/users`
- **Pagination Strategy**: Page number with `page` and `page-size` parameters.
- **Data Selector**: `data`

### Projects
- **Endpoint Path**: `/v1/workspaces/{workspace_id}/projects`
- **Pagination Strategy**: Page number with `page` and `page-size` parameters.
- **Data Selector**: `data`

### Clients
- **Endpoint Path**: `/v1/workspaces/{workspace_id}/clients`
- **Pagination Strategy**: Page number with `page` and `page-size` parameters.
- **Data Selector**: `data`

### Tags
- **Endpoint Path**: `/v1/workspaces/{workspace_id}/tags`
- **Pagination Strategy**: Page number with `page` and `page-size` parameters.
- **Data Selector**: `data`

### User Groups
- **Endpoint Path**: `/v1/workspaces/{workspace_id}/user-groups`
- **Pagination Strategy**: Page number with `page` and `page-size` parameters.
- **Data Selector**: `data`

### Time Entries
- **Endpoint Path**: `/v1/workspaces/{workspace_id}/user/{user_id}/time-entries`
- **Pagination Strategy**: Page number with `page` and `page-size` parameters.
- **Data Selector**: `data`

### Tasks
- **Endpoint Path**: `/v1/workspaces/{workspace_id}/projects/{project_id}/tasks`
- **Pagination Strategy**: Page number with `page` and `page-size` parameters.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a schema that defines the fields, types, and nullability. The field names are neutral and generalized to ensure compatibility with various data models. For example, user-related fields include `id`, `name`, `email`, and `status`, while project-related fields include `id`, `name`, `clientId`, and `archived`.