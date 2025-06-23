# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and schema. The API requires authentication via an API key, which is included in the request headers.

# Configuration Example

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
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects",
                "data_selector": "data"
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/v1/projects/{parent_id}/tasks",
                "data_selector": "data"
            }
        },
        {
            "name": "time_records",
            "endpoint": {
                "path": "/v1/projects/{parent_id}/time",
                "data_selector": "data"
            }
        },
        {
            "name": "clients",
            "endpoint": {
                "path": "/v1/clients",
                "data_selector": "data"
            }
        },
        {
            "name": "time",
            "endpoint": {
                "path": "/v1/team/time",
                "data_selector": "data"
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/team/users",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Configuration**: The API key is stored securely and injected into the request headers under the name `X-Api-Key`.

# Resources

### Projects
- **Endpoint Path**: `/v1/projects`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Tasks
- **Endpoint Path**: `/v1/projects/{parent_id}/tasks`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Time Records
- **Endpoint Path**: `/v1/projects/{parent_id}/time`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Clients
- **Endpoint Path**: `/v1/clients`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Time
- **Endpoint Path**: `/v1/team/time`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Users
- **Endpoint Path**: `/v1/team/users`
- **Primary Key**: `id`
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a defined schema with fields, types, and nullability. The schemas are generalized and do not include any proprietary field names. For example, fields like `id`, `name`, `createdAt`, and `status` are used across different resources.