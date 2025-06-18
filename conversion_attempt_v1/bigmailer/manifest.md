# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration allows for seamless data extraction from various resources such as brands, contacts, lists, fields, message types, segments, bulk campaigns, transactional campaigns, suppression lists, and users. This data can be used for analysis, reporting, or automation tasks.

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
            "name": "brands",
            "endpoint": {
                "path": "/brands",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "not response.get('has_more', False)"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/brands/{brand_id}/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "not response.get('has_more', False)"
                }
            }
        },
        {
            "name": "lists",
            "endpoint": {
                "path": "/brands/{brand_id}/lists",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "not response.get('has_more', False)"
                }
            }
        },
        {
            "name": "fields",
            "endpoint": {
                "path": "/brands/{brand_id}/fields",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "not response.get('has_more', False)"
                }
            }
        },
        {
            "name": "message_types",
            "endpoint": {
                "path": "/brands/{brand_id}/message-types",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "not response.get('has_more', False)"
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/brands/{brand_id}/segments",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "not response.get('has_more', False)"
                }
            }
        },
        {
            "name": "bulk_campaigns",
            "endpoint": {
                "path": "/brands/{brand_id}/bulk-campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "not response.get('has_more', False)"
                }
            }
        },
        {
            "name": "transactional_campaigns",
            "endpoint": {
                "path": "/brands/{brand_id}/transactional-campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "not response.get('has_more', False)"
                }
            }
        },
        {
            "name": "suppression_lists",
            "endpoint": {
                "path": "/brands/{brand_id}/suppression-lists",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "not response.get('has_more', False)"
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
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "not response.get('has_more', False)"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the request header.

## Resources

### Brands
- **Endpoint Path**: `/brands`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.
- **Data Selector**: `data`

### Contacts
- **Endpoint Path**: `/brands/{brand_id}/contacts`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.
- **Data Selector**: `data`

### Lists
- **Endpoint Path**: `/brands/{brand_id}/lists`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.
- **Data Selector**: `data`

### Fields
- **Endpoint Path**: `/brands/{brand_id}/fields`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.
- **Data Selector**: `data`

### Message Types
- **Endpoint Path**: `/brands/{brand_id}/message-types`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.
- **Data Selector**: `data`

### Segments
- **Endpoint Path**: `/brands/{brand_id}/segments`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.
- **Data Selector**: `data`

### Bulk Campaigns
- **Endpoint Path**: `/brands/{brand_id}/bulk-campaigns`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.
- **Data Selector**: `data`

### Transactional Campaigns
- **Endpoint Path**: `/brands/{brand_id}/transactional-campaigns`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.
- **Data Selector**: `data`

### Suppression Lists
- **Endpoint Path**: `/brands/{brand_id}/suppression-lists`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.
- **Data Selector**: `data`

### Users
- **Endpoint Path**: `/users`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a schema that defines the fields, types, and nullability. The primary key for each resource is `id`. The schemas are designed to be flexible and accommodate various data types, ensuring compatibility with different data structures.