# Overview

This document provides a configuration guide for integrating with a third-party scheduling API using dltHub's REST API source format. The integration allows for syncing various resources such as organization members, groups, event types, and scheduled events. The API supports pagination and incremental data synchronization based on timestamps.

# Configuration Example

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
        "primary_key": "uri",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "event_types",
            "endpoint": {
                "path": "/event_types",
                "data_selector": "collection",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page_token",
                    "cursor_path": "pagination.next_page_token"
                }
            }
        },
        {
            "name": "api_user",
            "endpoint": {
                "path": "/users/me",
                "data_selector": "resource"
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/groups",
                "data_selector": "collection",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page_token",
                    "cursor_path": "pagination.next_page_token"
                }
            }
        },
        {
            "name": "organization_memberships",
            "endpoint": {
                "path": "/organization_memberships",
                "data_selector": "collection",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page_token",
                    "cursor_path": "pagination.next_page_token"
                }
            }
        },
        {
            "name": "scheduled_events",
            "endpoint": {
                "path": "/scheduled_events",
                "data_selector": "collection",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page_token",
                    "cursor_path": "pagination.next_page_token"
                }
            }
        },
        {
            "name": "event_invitees",
            "endpoint": {
                "path": "/scheduled_events/{scheduled_event_id}/invitees",
                "data_selector": "collection",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page_token",
                    "cursor_path": "pagination.next_page_token"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from dlt.secrets["api_key"]

# Resources

### Event Types
- **Endpoint Path**: `/event_types`
- **Data Selector**: `collection`
- **Pagination**: Cursor-based using `page_token`

### API User
- **Endpoint Path**: `/users/me`
- **Data Selector**: `resource`

### Groups
- **Endpoint Path**: `/groups`
- **Data Selector**: `collection`
- **Pagination**: Cursor-based using `page_token`

### Organization Memberships
- **Endpoint Path**: `/organization_memberships`
- **Data Selector**: `collection`
- **Pagination**: Cursor-based using `page_token`

### Scheduled Events
- **Endpoint Path**: `/scheduled_events`
- **Data Selector**: `collection`
- **Pagination**: Cursor-based using `page_token`

### Event Invitees
- **Endpoint Path**: `/scheduled_events/{scheduled_event_id}/invitees`
- **Data Selector**: `collection`
- **Pagination**: Cursor-based using `page_token`

# Error Handling

- **Retry Logic**: Implement retry on status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a schema that defines the fields, types, and nullability. The schemas are consistent with the API's response structure and use neutral field names. For example, the `event_types` resource includes fields like `type`, `active`, `created_at`, and `uri`.