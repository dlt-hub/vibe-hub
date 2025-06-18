# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports seamless data synchronization between various resources and destinations, enabling real-time data access and automated workflows.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["access_token"]
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
                "path": "/v9/projects",
                "data_selector": "projects",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "until",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/v2/teams",
                "data_selector": "teams",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "until",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "user",
            "endpoint": {
                "path": "/v2/user",
                "data_selector": "user",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "until",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "deployments",
            "endpoint": {
                "path": "/v6/deployments",
                "data_selector": "deployments",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "until",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "environments",
            "endpoint": {
                "path": "/v9/projects/{project}/env",
                "data_selector": "envs",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "until",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "auth_tokens",
            "endpoint": {
                "path": "/v5/user/tokens",
                "data_selector": "tokens",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "until",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "aliases",
            "endpoint": {
                "path": "/v4/aliases",
                "data_selector": "aliases",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "until",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "deployment_events",
            "endpoint": {
                "path": "/v3/deployments/{deployment}/events",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "until",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "teams_member",
            "endpoint": {
                "path": "/v2/teams/{team_id}/members",
                "data_selector": "members",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "until",
                    "cursor_path": "pagination.next"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `access_token` stored in `dlt.secrets`.

# Resources

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized paths for each resource.
- **Pagination Strategy**: Cursor-based pagination with a limit of 100 records per request.
- **Data Selector**: Specifies the field path to extract data from the response.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Factor**: Use a backoff factor of 2 for retries.
- **Max Retries**: Set a maximum of 3 retries for failed requests.

# Schema

- **Fields**: Use neutral field names such as `id`, `createdAt`, `updatedAt`, etc.
- **Types**: Ensure consistency with the original schema, using types like `string`, `number`, `boolean`, and `array`.
- **Nullability**: Allow fields to be nullable where applicable.