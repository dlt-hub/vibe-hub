# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for seamless data extraction from the API, enabling teams to sync and analyze information across platforms. The API supports various resources, each with its own endpoint and pagination strategy.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
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
            "name": "users",
            "endpoint": {
                "path": "/v1/user",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page",
                    "cursor_path": "next.page",
                    "stop_condition": "response.get('next') is None"
                }
            }
        },
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v1/orgs",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page",
                    "cursor_path": "next.page",
                    "stop_condition": "response.get('next') is None"
                }
            }
        },
        {
            "name": "insights_traffic",
            "endpoint": {
                "path": "/v1/spaces/{space_id}/insights/traffic",
                "data_selector": "views",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page",
                    "cursor_path": "next.page",
                    "stop_condition": "response.get('next') is None"
                }
            }
        },
        {
            "name": "content",
            "endpoint": {
                "path": "/v1/spaces/{space_id}/content",
                "data_selector": "pages",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page",
                    "cursor_path": "next.page",
                    "stop_condition": "response.get('next') is None"
                }
            }
        },
        {
            "name": "org_members",
            "endpoint": {
                "path": "/v1/orgs/{organization}/members",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page",
                    "cursor_path": "next.page",
                    "stop_condition": "response.get('next') is None"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `access_token` stored in `dlt.secrets`.

# Resources

### Users
- **Endpoint Path**: `/v1/user`
- **Data Selector**: `data`
- **Pagination**: Cursor-based with `page` as the cursor parameter.

### Organizations
- **Endpoint Path**: `/v1/orgs`
- **Data Selector**: `items`
- **Pagination**: Cursor-based with `page` as the cursor parameter.

### Insights Traffic
- **Endpoint Path**: `/v1/spaces/{space_id}/insights/traffic`
- **Data Selector**: `views`
- **Pagination**: Cursor-based with `page` as the cursor parameter.

### Content
- **Endpoint Path**: `/v1/spaces/{space_id}/content`
- **Data Selector**: `pages`
- **Pagination**: Cursor-based with `page` as the cursor parameter.

### Organization Members
- **Endpoint Path**: `/v1/orgs/{organization}/members`
- **Data Selector**: `items`
- **Pagination**: Cursor-based with `page` as the cursor parameter.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

### Users
- **Fields**: `displayName`, `email`, `id`, `object`, `photoURL`, `urls`
- **Types**: String, Object, Nullable

### Organizations
- **Fields**: `type`, `createdAt`, `defaultRole`, `emailDomains`, `id`, `inviteLinks`, `object`, `plan`, `title`, `urls`, `useCase`
- **Types**: String, Array, Boolean, Nullable

### Insights Traffic
- **Fields**: `count`, `timestamp`
- **Types**: Number, String

### Content
- **Fields**: `type`, `cover`, `documentId`, `icon`, `id`, `kind`, `layout`, `pages`, `path`, `slug`, `title`, `urls`
- **Types**: String, Object, Array, Nullable

### Organization Members
- **Fields**: `disabled`, `id`, `joinedAt`, `lastSeenAt`, `object`, `role`, `spaces`, `sso`, `teams`, `user`
- **Types**: Boolean, String, Number, Object, Nullable

This configuration provides a comprehensive guide to setting up a REST API source using dltHub, ensuring a clean and efficient data integration process.