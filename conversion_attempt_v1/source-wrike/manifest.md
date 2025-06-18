# Overview

This document provides a configuration example for integrating with a third-party REST API using dltHub's REST API source format. The integration supports multiple resources, each with its own endpoint and schema. The API uses bearer token authentication and supports cursor-based pagination.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v4",
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
            "name": "custom_fields",
            "endpoint": {
                "path": "/customfields",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageToken"
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
                    "cursor_param": "nextPageToken"
                }
            }
        },
        {
            "name": "workflows",
            "endpoint": {
                "path": "/workflows",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageToken"
                }
            }
        },
        {
            "name": "comments",
            "endpoint": {
                "path": "/comments",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageToken"
                }
            }
        },
        {
            "name": "folders",
            "endpoint": {
                "path": "/folders",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageToken"
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageToken"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer Token
- **Token**: Stored securely in `dlt.secrets["access_token"]`

## Resources

### Custom Fields
- **Endpoint Path**: `/customfields`
- **Pagination**: Cursor-based using `nextPageToken`
- **Data Selector**: `data`

### Contacts
- **Endpoint Path**: `/contacts`
- **Pagination**: Cursor-based using `nextPageToken`
- **Data Selector**: `data`

### Workflows
- **Endpoint Path**: `/workflows`
- **Pagination**: Cursor-based using `nextPageToken`
- **Data Selector**: `data`

### Comments
- **Endpoint Path**: `/comments`
- **Pagination**: Cursor-based using `nextPageToken`
- **Data Selector**: `data`

### Folders
- **Endpoint Path**: `/folders`
- **Pagination**: Cursor-based using `nextPageToken`
- **Data Selector**: `data`

### Tasks
- **Endpoint Path**: `/tasks`
- **Pagination**: Cursor-based using `nextPageToken`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implemented with exponential backoff for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a schema defined with fields such as `id`, `title`, `createdDate`, and others. The schemas are flexible, allowing for additional properties and null values where applicable. Field names are generalized to ensure vendor neutrality.