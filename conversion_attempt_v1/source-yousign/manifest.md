# Overview

This document provides a configuration example for integrating with a third-party API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API uses a bearer token for authentication and supports incremental synchronization based on datetime fields.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3/",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "signature_requests",
            "endpoint": {
                "path": "/signature_requests",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "meta.next_cursor"
                }
            }
        },
        {
            "name": "signature_requests_followers",
            "endpoint": {
                "path": "/signature_requests/{sign_req}/followers",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "meta.next_cursor"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "meta.next_cursor"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "meta.next_cursor"
                }
            }
        },
        {
            "name": "workspaces",
            "endpoint": {
                "path": "/workspaces",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "meta.next_cursor"
                }
            }
        },
        {
            "name": "electronic_seal_images",
            "endpoint": {
                "path": "/electronic_seal_images",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "meta.next_cursor"
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/templates",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "meta.next_cursor"
                }
            }
        },
        {
            "name": "labels",
            "endpoint": {
                "path": "/labels",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "meta.next_cursor"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Signature Requests
- **Endpoint Path**: `/signature_requests`
- **Pagination**: Cursor-based using `after` parameter and `meta.next_cursor` path
- **Data Selector**: `data`

### Signature Requests Followers
- **Endpoint Path**: `/signature_requests/{sign_req}/followers`
- **Pagination**: Cursor-based using `after` parameter and `meta.next_cursor` path
- **Data Selector**: `data`

### Contacts
- **Endpoint Path**: `/contacts`
- **Pagination**: Cursor-based using `after` parameter and `meta.next_cursor` path
- **Data Selector**: `data`

### Users
- **Endpoint Path**: `/users`
- **Pagination**: Cursor-based using `after` parameter and `meta.next_cursor` path
- **Data Selector**: `data`

### Workspaces
- **Endpoint Path**: `/workspaces`
- **Pagination**: Cursor-based using `after` parameter and `meta.next_cursor` path
- **Data Selector**: `data`

### Electronic Seal Images
- **Endpoint Path**: `/electronic_seal_images`
- **Pagination**: Cursor-based using `after` parameter and `meta.next_cursor` path
- **Data Selector**: `data`

### Templates
- **Endpoint Path**: `/templates`
- **Pagination**: Cursor-based using `after` parameter and `meta.next_cursor` path
- **Data Selector**: `data`

### Labels
- **Endpoint Path**: `/labels`
- **Pagination**: Cursor-based using `after` parameter and `meta.next_cursor` path
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504) with a maximum of 3 retries and a backoff factor of 2.

## Schema

Each resource has a defined schema with fields such as `id`, `created_at`, and other relevant attributes. The schemas are designed to be flexible, allowing for additional properties and null values where applicable.