# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and schema. The API uses an API key for authentication and supports cursor-based pagination for some resources.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
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
            "name": "accounts",
            "endpoint": {
                "path": "/accounts",
                "data_selector": "accounts"
            }
        },
        {
            "name": "admin_attributes",
            "endpoint": {
                "path": "/admin/model/account/attributes",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "page_size": 5,
                    "cursor_param": "scrollId"
                }
            }
        },
        {
            "name": "articles",
            "endpoint": {
                "path": "/articles",
                "data_selector": "articleExternalViewList"
            }
        },
        {
            "name": "feature",
            "endpoint": {
                "path": "/feature",
                "data_selector": "features"
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/segment",
                "data_selector": "segments"
            }
        },
        {
            "name": "kcbot",
            "endpoint": {
                "path": "/kcbot",
                "data_selector": "kcList"
            }
        },
        {
            "name": "user_attributes",
            "endpoint": {
                "path": "/admin/model/user/attributes",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "page_size": 5,
                    "cursor_param": "scrollId"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "users"
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the request header.

## Resources

### Accounts
- **Endpoint Path**: `/accounts`
- **Data Selector**: `accounts`
- **Primary Key**: `id`

### Admin Attributes
- **Endpoint Path**: `/admin/model/account/attributes`
- **Data Selector**: `[]`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `scrollId` as the cursor parameter.

### Articles
- **Endpoint Path**: `/articles`
- **Data Selector**: `articleExternalViewList`
- **Primary Key**: `id`

### Feature
- **Endpoint Path**: `/feature`
- **Data Selector**: `features`
- **Primary Key**: `id`

### Segments
- **Endpoint Path**: `/segment`
- **Data Selector**: `segments`
- **Primary Key**: `id`

### KCBot
- **Endpoint Path**: `/kcbot`
- **Data Selector**: `kcList`
- **Primary Key**: `id`

### User Attributes
- **Endpoint Path**: `/admin/model/user/attributes`
- **Data Selector**: `[]`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `scrollId` as the cursor parameter.

### Users
- **Endpoint Path**: `/users`
- **Data Selector**: `users`
- **Primary Key**: `id`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a defined schema with fields, types, and nullability. The schemas are generalized and do not include any proprietary field names. For example, fields like `id`, `name`, `createdDate`, and `modifiedDate` are used across different resources.