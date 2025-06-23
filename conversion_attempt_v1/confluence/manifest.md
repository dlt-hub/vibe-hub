# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, each with its own endpoint and schema. The API uses basic authentication and supports pagination for efficient data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["email"],
            "password": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "audit",
            "endpoint": {
                "path": "/v1/audit",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 25,
                    "offset_param": "start"
                }
            }
        },
        {
            "name": "blog_posts",
            "endpoint": {
                "path": "/v2/blogposts",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 25,
                    "offset_param": "start"
                }
            }
        },
        {
            "name": "group",
            "endpoint": {
                "path": "/v1/group",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 25,
                    "offset_param": "start"
                }
            }
        },
        {
            "name": "pages",
            "endpoint": {
                "path": "/v2/pages",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 25,
                    "offset_param": "start"
                }
            }
        },
        {
            "name": "space",
            "endpoint": {
                "path": "/v2/spaces",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 25,
                    "offset_param": "start"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**:
  - `email`: Your login email
  - `api_token`: Your API token

## Resources

### Audit
- **Endpoint Path**: `/v1/audit`
- **Primary Key**: `creationDate`
- **Pagination**: Offset with `start` parameter
- **Data Selector**: `results`

### Blog Posts
- **Endpoint Path**: `/v2/blogposts`
- **Primary Key**: `id`
- **Pagination**: Offset with `start` parameter
- **Data Selector**: `results`

### Group
- **Endpoint Path**: `/v1/group`
- **Primary Key**: `id`
- **Pagination**: Offset with `start` parameter
- **Data Selector**: `results`

### Pages
- **Endpoint Path**: `/v2/pages`
- **Primary Key**: `id`
- **Pagination**: Offset with `start` parameter
- **Data Selector**: `results`

### Space
- **Endpoint Path**: `/v2/spaces`
- **Primary Key**: `id`
- **Pagination**: Offset with `start` parameter
- **Data Selector**: `results`

## Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Audit
- **Fields**: `description`, `affectedObject`, `associatedObjects`, `author`, `category`, `changedValues`, `creationDate`, `remoteAddress`, `summary`, `superAdmin`, `sysAdmin`

### Blog Posts
- **Fields**: `version`, `id`, `status`, `title`, `spaceId`, `authorId`, `createdAt`, `body`, `_links`

### Group
- **Fields**: `type`, `_links`, `id`, `name`

### Pages
- **Fields**: `version`, `id`, `status`, `title`, `spaceId`, `parentId`, `parentType`, `position`, `authorId`, `ownerId`, `lastOwnerId`, `createdAt`, `body`, `_links`

### Space
- **Fields**: `type`, `description`, `id`, `key`, `name`, `status`, `authorId`, `createdAt`, `homepageId`, `icon`, `_links`

This configuration provides a comprehensive setup for integrating with a third-party analytics API using dltHub's REST API source format, ensuring efficient data synchronization and management.