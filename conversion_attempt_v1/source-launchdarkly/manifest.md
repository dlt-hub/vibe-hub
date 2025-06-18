# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and schema. The API uses an API key for authentication and supports offset-based pagination.

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
            "api_key": dlt.secrets["access_token"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge",
        "endpoint": {
            "params": {
                "limit": 20
            }
        }
    },
    "resources": [
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "environments",
            "endpoint": {
                "path": "/v1/projects/{project_key}/environments",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "metrics",
            "endpoint": {
                "path": "/v1/metrics/{project_key}",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "members",
            "endpoint": {
                "path": "/v1/members",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "auditlog",
            "endpoint": {
                "path": "/v1/auditlog",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "flags",
            "endpoint": {
                "path": "/v1/flags/{project_key}",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Secrets**: The API key is stored in `dlt.secrets["access_token"]`.
- **Location**: The API key is included in the request header.

# Resources

### Projects
- **Endpoint Path**: `/v1/projects`
- **Pagination**: Offset-based with `limit` and `offset` parameters.
- **Data Selector**: `items`

### Environments
- **Endpoint Path**: `/v1/projects/{project_key}/environments`
- **Pagination**: Offset-based with `limit` and `offset` parameters.
- **Data Selector**: `items`

### Metrics
- **Endpoint Path**: `/v1/metrics/{project_key}`
- **Pagination**: Offset-based with `limit` and `offset` parameters.
- **Data Selector**: `items`

### Members
- **Endpoint Path**: `/v1/members`
- **Pagination**: Offset-based with `limit` and `offset` parameters.
- **Data Selector**: `items`

### Audit Log
- **Endpoint Path**: `/v1/auditlog`
- **Pagination**: Offset-based with `limit` and `offset` parameters.
- **Data Selector**: `items`

### Flags
- **Endpoint Path**: `/v1/flags/{project_key}`
- **Pagination**: Offset-based with `limit` and `offset` parameters.
- **Data Selector**: `items`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a defined schema with fields such as `id`, `name`, `key`, and `tags`. The schemas are flexible, allowing for additional properties. Field types include strings, integers, booleans, objects, and arrays, with support for nullable values.