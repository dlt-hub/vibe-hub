# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as teams, users, cycles, issues, comments, projects, and more. Each resource is configured to handle pagination, authentication, and data extraction in a vendor-neutral manner.

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
            "name": "teams",
            "endpoint": {
                "path": "/v1/teams",
                "data_selector": "data.teams.nodes",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "pageInfo.endCursor",
                    "stop_condition": "pageInfo.hasNextPage is false"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data.users.nodes",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "pageInfo.endCursor",
                    "stop_condition": "pageInfo.hasNextPage is false"
                }
            }
        },
        {
            "name": "cycles",
            "endpoint": {
                "path": "/v1/cycles",
                "data_selector": "data.cycles.nodes",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "pageInfo.endCursor",
                    "stop_condition": "pageInfo.hasNextPage is false"
                }
            }
        },
        {
            "name": "issues",
            "endpoint": {
                "path": "/v1/issues",
                "data_selector": "data.issues.nodes",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "pageInfo.endCursor",
                    "stop_condition": "pageInfo.hasNextPage is false"
                }
            }
        },
        {
            "name": "comments",
            "endpoint": {
                "path": "/v1/comments",
                "data_selector": "data.comments.nodes",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "pageInfo.endCursor",
                    "stop_condition": "pageInfo.hasNextPage is false"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects",
                "data_selector": "data.projects.nodes",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "pageInfo.endCursor",
                    "stop_condition": "pageInfo.hasNextPage is false"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "data.customers.nodes",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "pageInfo.endCursor",
                    "stop_condition": "pageInfo.hasNextPage is false"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

## Resources

### Teams
- **Endpoint Path**: `/v1/teams`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data.teams.nodes`

### Users
- **Endpoint Path**: `/v1/users`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data.users.nodes`

### Cycles
- **Endpoint Path**: `/v1/cycles`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data.cycles.nodes`

### Issues
- **Endpoint Path**: `/v1/issues`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data.issues.nodes`

### Comments
- **Endpoint Path**: `/v1/comments`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data.comments.nodes`

### Projects
- **Endpoint Path**: `/v1/projects`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data.projects.nodes`

### Customers
- **Endpoint Path**: `/v1/customers`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data.customers.nodes`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

- **Fields**: Each resource has a schema defined with fields such as `id`, `name`, `createdAt`, `updatedAt`, etc.
- **Types**: Fields are typed as `string`, `number`, `boolean`, or `array`.
- **Nullability**: Fields can be nullable, indicated by `"null"` in the type definition.