# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API requires authentication and supports data retrieval through various endpoints.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["secret_token"],
            "password": dlt.secrets["basic_auth_header"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "accounts",
            "endpoint": {
                "path": "/accounts",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "from",
                    "cursor_value": "{{ response.next }}"
                }
            }
        },
        {
            "name": "admins",
            "endpoint": {
                "path": "/admins",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "from",
                    "cursor_value": "{{ response.next }}"
                }
            }
        },
        {
            "name": "conversations",
            "endpoint": {
                "path": "/conversations",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "from",
                    "cursor_value": "{{ response.next }}"
                }
            }
        },
        {
            "name": "notes",
            "endpoint": {
                "path": "/notes",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "from",
                    "cursor_value": "{{ response.next }}"
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/tasks",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "from",
                    "cursor_value": "{{ response.next }}"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "from",
                    "cursor_value": "{{ response.next }}"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**:
  - `secret_token`: The username for authentication.
  - `basic_auth_header`: The password for authentication.

# Resources

Each resource is configured with a specific endpoint path and pagination strategy. The data is selected from the "results" field in the response.

- **Resource Name**: accounts, admins, conversations, notes, tasks, users
- **Endpoint Path**: `/accounts`, `/admins`, `/conversations`, `/notes`, `/tasks`, `/users`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based pagination with a limit of 100 records per page.
- **Data Selector**: `results`

# Error Handling

The configuration does not explicitly define error handling strategies. It is recommended to implement retry logic and handle common HTTP status codes such as 429 (Too Many Requests) and 500 (Internal Server Error) to ensure robust data retrieval.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for all resources is `id`. The schema includes various fields with types such as string, number, and array, with support for nullable values.