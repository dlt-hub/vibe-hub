# Overview

This document provides a configuration guide for integrating with a third-party time tracking and management API using the dltHub REST API source. The integration allows for the retrieval of various resources such as time entries, organizations, users, groups, workspaces, clients, projects, and tasks. The API supports basic authentication and requires specific parameters for data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_token"],
            "password": "api_token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "time_entries",
            "endpoint": {
                "path": "/v1/me/time_entries",
                "data_selector": "data"
            }
        },
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}",
                "data_selector": "data"
            }
        },
        {
            "name": "organizations_users",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}/users",
                "data_selector": "data"
            }
        },
        {
            "name": "organizations_groups",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}/groups",
                "data_selector": "data"
            }
        },
        {
            "name": "workspace",
            "endpoint": {
                "path": "/v1/workspaces/{workspace_id}/statistics",
                "data_selector": "data"
            }
        },
        {
            "name": "workspace_clients",
            "endpoint": {
                "path": "/v1/workspaces/{workspace_id}/clients",
                "data_selector": "data"
            }
        },
        {
            "name": "workspace_projects",
            "endpoint": {
                "path": "/v1/workspaces/{workspace_id}/projects",
                "data_selector": "data"
            }
        },
        {
            "name": "workspace_tasks",
            "endpoint": {
                "path": "/v1/workspaces/{workspace_id}/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 50
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_token`: The API token used as the username for authentication.
  - `password`: Use the string "api_token" as the password.

## Resources

### Time Entries
- **Endpoint Path**: `/v1/me/time_entries`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Organizations
- **Endpoint Path**: `/v1/organizations/{organization_id}`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Organizations Users
- **Endpoint Path**: `/v1/organizations/{organization_id}/users`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Organizations Groups
- **Endpoint Path**: `/v1/organizations/{organization_id}/groups`
- **Primary Key**: `group_id`
- **Data Selector**: `data`

### Workspace
- **Endpoint Path**: `/v1/workspaces/{workspace_id}/statistics`
- **Data Selector**: `data`

### Workspace Clients
- **Endpoint Path**: `/v1/workspaces/{workspace_id}/clients`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Workspace Projects
- **Endpoint Path**: `/v1/workspaces/{workspace_id}/projects`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Workspace Tasks
- **Endpoint Path**: `/v1/workspaces/{workspace_id}/tasks`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination**: 
  - **Type**: Page Number
  - **Page Parameter**: `page`
  - **Limit Parameter**: `per_page`
  - **Limit**: 50

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with fields, types, and nullability. Use neutral field names such as `customer_id` or `user_key` instead of branded terms. The schema is consistent with the provided YAML configuration and is generalized for public use.