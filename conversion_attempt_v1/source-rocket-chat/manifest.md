# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as teams, rooms, channels, roles, subscriptions, and users, each with its own endpoint and schema.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/v1",
        "auth": {
            "type": "api_key",
            "name": "X-Auth-Token",
            "api_key": dlt.secrets["api_token"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "teams",
            "endpoint": {
                "path": "/teams.list",
                "data_selector": "teams",
                "paginator": {
                    "type": "offset",
                    "limit": 1,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "rooms",
            "endpoint": {
                "path": "/rooms.get",
                "data_selector": "update"
            }
        },
        {
            "name": "channels",
            "endpoint": {
                "path": "/channels.list",
                "data_selector": "channels",
                "paginator": {
                    "type": "offset",
                    "limit": 1,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/roles.list",
                "data_selector": "roles"
            }
        },
        {
            "name": "subscriptions",
            "endpoint": {
                "path": "/subscriptions.get",
                "data_selector": "update"
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users.list",
                "data_selector": "users",
                "paginator": {
                    "type": "offset",
                    "limit": 1,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: 
  - `api_token`: The API token used for authentication.
  - `user_id`: The user ID required for making requests.

## Resources

### Teams
- **Endpoint Path**: `/teams.list`
- **Data Selector**: `teams`
- **Pagination**: Offset-based with `offset` parameter and limit of 1.

### Rooms
- **Endpoint Path**: `/rooms.get`
- **Data Selector**: `update`

### Channels
- **Endpoint Path**: `/channels.list`
- **Data Selector**: `channels`
- **Pagination**: Offset-based with `offset` parameter and limit of 1.

### Roles
- **Endpoint Path**: `/roles.list`
- **Data Selector**: `roles`

### Subscriptions
- **Endpoint Path**: `/subscriptions.get`
- **Data Selector**: `update`

### Users
- **Endpoint Path**: `/users.list`
- **Data Selector**: `users`
- **Pagination**: Offset-based with `offset` parameter and limit of 1.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a defined schema with fields such as `_id`, `name`, `description`, and others. The fields are generalized and support nullability. For example:

- **Teams Schema**:
  - `_id`: string
  - `name`: string
  - `numberOfUsers`: integer

- **Rooms Schema**:
  - `_id`: string
  - `broadcast`: boolean
  - `description`: string

- **Channels Schema**:
  - `_id`: string
  - `name`: string
  - `usersCount`: integer

- **Roles Schema**:
  - `_id`: string
  - `name`: string
  - `protected`: boolean

- **Subscriptions Schema**:
  - `_id`: string
  - `name`: string
  - `unread`: integer

- **Users Schema**:
  - `_id`: string
  - `username`: string
  - `status`: string

This configuration ensures a clean and vendor-neutral integration with the third-party analytics API, following dltHub's REST API source format.