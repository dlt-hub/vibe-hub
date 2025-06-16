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
- **Secrets or Config Values**: Use `dlt.secrets["api_token"]` to securely store and access the API token.
- **Location**: The API key is included in the request header.

## Resources

### User
- **Endpoint Path**: `/v1/user`
- **Data Selector**: `user`
- **Error Handling**: Retries on status codes 500 and 404 with exponential backoff.

### Team
- **Endpoint Path**: `/v1/team`
- **Data Selector**: `teams`
- **Error Handling**: Retries on status codes 500 and 404 with exponential backoff.

### List
- **Endpoint Path**: `/v1/folder/{folder_id}/list`
- **Data Selector**: `lists`
- **Error Handling**: Retries on status codes 500 and 404 with exponential backoff.

### Task
- **Endpoint Path**: `/v1/list/{list_id}/task`
- **Data Selector**: `tasks`
- **Pagination Strategy**: Offset pagination with a limit of 1.
- **Error Handling**: Retries on status codes 500 and 404 with exponential backoff.

## Error Handling

- **Retry Logic**: The configuration includes retry logic for HTTP status codes 500 and 404. The system will attempt up to 3 retries with an exponential backoff factor of 2.

## Schema

The schema for each resource is defined with neutral field names and types. For example, the `user` resource includes fields like `id`, `username`, and `profilePicture`, all of which are nullable and have appropriate data types.