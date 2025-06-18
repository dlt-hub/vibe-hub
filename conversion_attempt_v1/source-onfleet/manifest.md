# Overview

This document provides a configuration example for integrating with a third-party logistics API using dltHub's REST API source format. The integration allows for data ingestion from various resources such as workers, administrators, teams, hubs, tasks, and containers. The API requires authentication via an API key and supports pagination for certain resources.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "workers",
            "endpoint": {
                "path": "/api/v2/workers",
                "data_selector": "data"
            }
        },
        {
            "name": "administrators",
            "endpoint": {
                "path": "/api/v2/admins",
                "data_selector": "data"
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/api/v2/teams",
                "data_selector": "data"
            }
        },
        {
            "name": "hubs",
            "endpoint": {
                "path": "/api/v2/hubs",
                "data_selector": "data"
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/api/v2/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "lastId",
                    "cursor_path": "lastId"
                }
            }
        },
        {
            "name": "containers",
            "endpoint": {
                "path": "/api/v2/containers/workers/{{ stream_slice.worker_id }}",
                "data_selector": "data"
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: The API key used as the username for authentication.
  - `password`: A placeholder password, typically set to an empty string.

## Resources

### Workers
- **Endpoint Path**: `/api/v2/workers`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Administrators
- **Endpoint Path**: `/api/v2/admins`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Teams
- **Endpoint Path**: `/api/v2/teams`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Hubs
- **Endpoint Path**: `/api/v2/hubs`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Tasks
- **Endpoint Path**: `/api/v2/tasks`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination**: 
  - **Type**: Cursor
  - **Cursor Parameter**: `lastId`
  - **Cursor Path**: `lastId`

### Containers
- **Endpoint Path**: `/api/v2/containers/workers/{{ stream_slice.worker_id }}`
- **Primary Key**: `id`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with neutral field names and types. For example, the `workers` resource includes fields such as `id`, `name`, `phone`, and `teams`, with appropriate data types and nullability. Each resource's schema is consistent with the provided YAML configuration and uses generalized field names.