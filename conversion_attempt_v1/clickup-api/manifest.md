# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and schema. The API requires authentication via an API key, and supports error handling and pagination strategies.

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
            "api_key": dlt.secrets["api_token"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "user",
            "endpoint": {
                "path": "/v1/user",
                "data_selector": "user",
                "error_handler": {
                    "retry_on_status_codes": [500, 404],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "team",
            "endpoint": {
                "path": "/v1/team",
                "data_selector": "teams",
                "error_handler": {
                    "retry_on_status_codes": [500, 404],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "list",
            "endpoint": {
                "path": "/v1/folder/{folder_id}/list",
                "data_selector": "lists",
                "error_handler": {
                    "retry_on_status_codes": [500, 404],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "task",
            "endpoint": {
                "path": "/v1/list/{list_id}/task",
                "data_selector": "tasks",
                "paginator": {
                    "type": "offset",
                    "limit": 1
                },
                "error_handler": {
                    "retry_on_status_codes": [500, 404],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_token"]`
- **Location**: Header

## Resources

### User
- **Endpoint Path**: `/v1/user`
- **Data Selector**: `user`
- **Primary Key**: `id`
- **Error Handling**: Retries on 500 and 404 status codes

### Team
- **Endpoint Path**: `/v1/team`
- **Data Selector**: `teams`
- **Primary Key**: `id`
- **Error Handling**: Retries on 500 and 404 status codes

### List
- **Endpoint Path**: `/v1/folder/{folder_id}/list`
- **Data Selector**: `lists`
- **Primary Key**: `id`
- **Error Handling**: Retries on 500 and 404 status codes

### Task
- **Endpoint Path**: `/v1/list/{list_id}/task`
- **Data Selector**: `tasks`
- **Primary Key**: `id`
- **Pagination**: Offset with limit of 1
- **Error Handling**: Retries on 500 and 404 status codes

## Error Handling

- **Retry Logic**: Retries on HTTP status codes 500 and 404
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### User
- **Fields**: `color`, `id`, `profilePicture`, `username`
- **Types**: String, Integer, Nullable

### Team
- **Fields**: `avatar`, `color`, `id`, `members`, `name`
- **Types**: String, Array, Object, Nullable

### List
- **Fields**: `archived`, `assignee`, `content`, `due_date`, `folder`, `id`, `name`, `orderindex`, `override_statuses`, `permission_level`, `priority`, `space`, `start_date`, `status`, `task_count`
- **Types**: String, Boolean, Object, Nullable

### Task
- **Fields**: `assignees`, `checklists`, `creator`, `date_closed`, `date_created`, `date_updated`, `due_date`, `folder`, `id`, `list`, `name`, `orderindex`, `parent`, `priority`, `space`, `start_date`, `status`, `tags`, `time_estimate`, `time_spent`, `url`
- **Types**: String, Array, Object, Nullable

This configuration provides a comprehensive setup for integrating with a third-party analytics API, ensuring robust error handling and efficient data retrieval through pagination.