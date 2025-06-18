# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and pagination strategy. The API uses OAuth2 for authentication and supports incremental synchronization based on datetime fields.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/oauth/token",
            "refresh_token": dlt.secrets["client_refresh_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "files",
            "endpoint": {
                "path": "/v1/files",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/v1/groups",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "folders",
            "endpoint": {
                "path": "/v1/folders",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "profile",
            "endpoint": {
                "path": "/v1/profiles/me",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "trashed",
            "endpoint": {
                "path": "/v1/trash",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "documents",
            "endpoint": {
                "path": "/v1/documents",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "annotations",
            "endpoint": {
                "path": "/v1/annotations",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "institutions",
            "endpoint": {
                "path": "/v1/institutions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "subject_areas",
            "endpoint": {
                "path": "/v1/subject_areas",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "catalog_search",
            "endpoint": {
                "path": "/v1/search/catalog",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "identifier_types",
            "endpoint": {
                "path": "/v1/identifier_types",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Client ID**: `dlt.secrets["client_id"]`
- **Client Secret**: `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/oauth/token`
- **Refresh Token**: `dlt.secrets["client_refresh_token"]`

## Resources

### Files
- **Endpoint Path**: `/v1/files`
- **Pagination**: Cursor-based with a limit of 10 items per page
- **Data Selector**: `data`

### Groups
- **Endpoint Path**: `/v1/groups`
- **Pagination**: Cursor-based with a limit of 10 items per page
- **Data Selector**: `data`

### Folders
- **Endpoint Path**: `/v1/folders`
- **Pagination**: Cursor-based with a limit of 10 items per page
- **Data Selector**: `data`

### Profile
- **Endpoint Path**: `/v1/profiles/me`
- **Pagination**: Cursor-based with a limit of 10 items per page
- **Data Selector**: `data`

### Trashed
- **Endpoint Path**: `/v1/trash`
- **Pagination**: Cursor-based with a limit of 10 items per page
- **Data Selector**: `data`

### Documents
- **Endpoint Path**: `/v1/documents`
- **Pagination**: Cursor-based with a limit of 10 items per page
- **Data Selector**: `data`

### Annotations
- **Endpoint Path**: `/v1/annotations`
- **Pagination**: Cursor-based with a limit of 10 items per page
- **Data Selector**: `data`

### Institutions
- **Endpoint Path**: `/v1/institutions`
- **Pagination**: Cursor-based with a limit of 10 items per page
- **Data Selector**: `data`

### Subject Areas
- **Endpoint Path**: `/v1/subject_areas`
- **Pagination**: Cursor-based with a limit of 10 items per page
- **Data Selector**: `data`

### Catalog Search
- **Endpoint Path**: `/v1/search/catalog`
- **Pagination**: Cursor-based with a limit of 10 items per page
- **Data Selector**: `data`

### Identifier Types
- **Endpoint Path**: `/v1/identifier_types`
- **Pagination**: Cursor-based with a limit of 10 items per page
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

- **Fields**: Use neutral field names such as `id`, `uuid`, `created`, `modified`, `last_modified`
- **Types**: Consistent with the YAML schema definitions
- **Nullability**: Allow additional properties as per the schema definitions