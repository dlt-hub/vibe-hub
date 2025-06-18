# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The API allows for the extraction of data related to board content, user activities, and collaboration metrics, which can be integrated with data warehouses for further analysis of team interactions and productivity.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
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
            "name": "boards",
            "endpoint": {
                "path": "/v2/boards",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "board_users",
            "endpoint": {
                "path": "/v2/boards/{board_id}/members",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "board_items",
            "endpoint": {
                "path": "/v2/boards/{board_id}/items",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "board_tags",
            "endpoint": {
                "path": "/v2/boards/{board_id}/tags",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "board_groups",
            "endpoint": {
                "path": "/v2/boards/{board_id}/groups",
                "data_selector": "data"
            }
        },
        {
            "name": "board_connectors",
            "endpoint": {
                "path": "/v2/boards/{board_id}/connectors",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
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

### Boards
- **Endpoint Path**: `/v2/boards`
- **Pagination**: Offset with `limit` of 50 and `offset_param` as "offset"
- **Data Selector**: `data`

### Board Users
- **Endpoint Path**: `/v2/boards/{board_id}/members`
- **Pagination**: Offset with `limit` of 50 and `offset_param` as "offset"
- **Data Selector**: `data`

### Board Items
- **Endpoint Path**: `/v2/boards/{board_id}/items`
- **Pagination**: Offset with `limit` of 50 and `offset_param` as "offset"
- **Data Selector**: `data`

### Board Tags
- **Endpoint Path**: `/v2/boards/{board_id}/tags`
- **Pagination**: Offset with `limit` of 50 and `offset_param` as "offset"
- **Data Selector**: `data`

### Board Groups
- **Endpoint Path**: `/v2/boards/{board_id}/groups`
- **Data Selector**: `data`

### Board Connectors
- **Endpoint Path**: `/v2/boards/{board_id}/connectors`
- **Pagination**: Offset with `limit` of 50 and `offset_param` as "offset"
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Boards
- **Fields**: `id`, `type`, `description`, `createdAt`, `createdBy`, `currentUserMembership`, `lastOpenedAt`, `lastOpenedBy`, `links`, `modifiedAt`, `modifiedBy`, `name`, `owner`, `permissionsPolicy`, `picture`, `policy`, `sharingPolicy`, `team`, `viewLink`
- **Primary Key**: `id`

### Board Users
- **Fields**: `id`, `type`, `links`, `name`, `role`
- **Primary Key**: `id`, `board_id`

### Board Items
- **Fields**: `id`, `type`, `createdAt`, `createdBy`, `data`, `geometry`, `isSupported`, `links`, `modifiedAt`, `modifiedBy`, `parent`, `position`, `style`
- **Primary Key**: `id`

### Board Tags
- **Fields**: `id`, `type`, `fillColor`, `links`, `title`
- **Primary Key**: `id`

### Board Groups
- **Fields**: `id`, `type`, `data`, `links`
- **Primary Key**: `id`

### Board Connectors
- **Fields**: `id`, `type`, `createdAt`, `createdBy`, `endItem`, `links`, `modifiedAt`, `modifiedBy`, `shape`, `startItem`, `style`
- **Primary Key**: `id`