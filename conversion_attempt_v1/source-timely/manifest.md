# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources such as events, clients, users, labels, and projects. The API uses bearer token authentication and supports pagination and incremental data synchronization.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["bearer_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/resource/events",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "clients",
            "endpoint": {
                "path": "/v1/resource/clients",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "offset_param": "offset",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/resource/users",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "offset_param": "offset",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "labels",
            "endpoint": {
                "path": "/v1/resource/labels",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "offset_param": "offset",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/resource/projects",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "offset_param": "offset",
                    "limit_param": "limit",
                    "limit": 1000
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `bearer_token` stored in dlt secrets for authentication.

# Resources

## Events
- **Endpoint Path**: `/v1/resource/events`
- **Pagination**: Page number-based with parameters `page` and `per_page`
- **Data Selector**: `data`

## Clients
- **Endpoint Path**: `/v1/resource/clients`
- **Pagination**: Offset-based with parameters `offset` and `limit`
- **Data Selector**: `data`

## Users
- **Endpoint Path**: `/v1/resource/users`
- **Pagination**: Offset-based with parameters `offset` and `limit`
- **Data Selector**: `data`

## Labels
- **Endpoint Path**: `/v1/resource/labels`
- **Pagination**: Offset-based with parameters `offset` and `limit`
- **Data Selector**: `data`

## Projects
- **Endpoint Path**: `/v1/resource/projects`
- **Pagination**: Offset-based with parameters `offset` and `limit`
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with fields such as `id`, `name`, `updated_at`, and other relevant attributes. The fields are generalized to ensure compatibility with various data types and structures.