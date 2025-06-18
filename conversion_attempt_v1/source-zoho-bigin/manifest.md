# Overview

This document provides a configuration example for integrating with a third-party CRM API using dltHub's REST API source. The integration allows for seamless data synchronization between the CRM and other platforms, automating data integration and ensuring real-time access to customer insights.

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
            "refresh_token": dlt.secrets["client_refresh_token"],
            "token_url": "https://accounts.example.com/oauth/v2/token"
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
                "path": "/v1/users",
                "data_selector": "users",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "modules",
            "endpoint": {
                "path": "/v1/settings/modules",
                "data_selector": "modules"
            }
        },
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v1/org",
                "data_selector": "org"
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/v1/settings/roles",
                "data_selector": "roles"
            }
        },
        {
            "name": "notes",
            "endpoint": {
                "path": "/v1/notes",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/settings/tags",
                "data_selector": "tags",
                "partition_router": {
                    "type": "list",
                    "values": ["Contacts", "Pipelines", "Companies", "Products", "Tasks", "Events", "Calls"],
                    "cursor_field": "module"
                }
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page_token",
                    "cursor_path": "info.next_page_token",
                    "stop_condition": "not response.get('info', {}).get('more_records', False)"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page_token",
                    "cursor_path": "info.next_page_token",
                    "stop_condition": "not response.get('info', {}).get('more_records', False)"
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/v1/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page_token",
                    "cursor_path": "info.next_page_token",
                    "stop_condition": "not response.get('info', {}).get('more_records', False)"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page_token",
                    "cursor_path": "info.next_page_token",
                    "stop_condition": "not response.get('info', {}).get('more_records', False)"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets**:
  - `client_id`
  - `client_secret`
  - `client_refresh_token`
- **Token URL**: `https://accounts.example.com/oauth/v2/token`

## Resources

### Users
- **Endpoint Path**: `/v1/users`
- **Data Selector**: `users`
- **Pagination**: Page number with `page` and `per_page` parameters, limit 200

### Modules
- **Endpoint Path**: `/v1/settings/modules`
- **Data Selector**: `modules`

### Organizations
- **Endpoint Path**: `/v1/org`
- **Data Selector**: `org`

### Roles
- **Endpoint Path**: `/v1/settings/roles`
- **Data Selector**: `roles`

### Notes
- **Endpoint Path**: `/v1/notes`
- **Data Selector**: `data`
- **Pagination**: Page number with `page` and `per_page` parameters, limit 200

### Tags
- **Endpoint Path**: `/v1/settings/tags`
- **Data Selector**: `tags`
- **Partition Router**: List with values for different modules

### Companies
- **Endpoint Path**: `/v1/accounts`
- **Data Selector**: `data`
- **Pagination**: Cursor with `page_token` parameter, using `info.next_page_token`

### Contacts
- **Endpoint Path**: `/v1/contacts`
- **Data Selector**: `data`
- **Pagination**: Cursor with `page_token` parameter, using `info.next_page_token`

### Tasks
- **Endpoint Path**: `/v1/tasks`
- **Data Selector**: `data`
- **Pagination**: Cursor with `page_token` parameter, using `info.next_page_token`

### Events
- **Endpoint Path**: `/v1/events`
- **Data Selector**: `data`
- **Pagination**: Page number with `page` and `per_page` parameters, limit 200

### Products
- **Endpoint Path**: `/v1/products`
- **Data Selector**: `data`
- **Pagination**: Cursor with `page_token` parameter, using `info.next_page_token`

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504)
- **Backoff Strategy**: Exponential backoff with a factor of 2
- **Max Retries**: 3 attempts

## Schema

Each resource has a schema defined with fields, types, and nullability. Field names are generalized to ensure neutrality and compatibility with various data models.