# Overview

This document provides a configuration guide for integrating with a generic third-party API that allows for the synchronization of various data types such as calendars, contacts, and messages. The integration supports multiple resources with different endpoints and pagination strategies, ensuring efficient data retrieval.

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
            "name": "calendars",
            "endpoint": {
                "path": "/v3/grants/{grant_id}/calendars",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "page_token",
                    "stop_condition": "not response.get('next_cursor', {})"
                }
            }
        },
        {
            "name": "connectors",
            "endpoint": {
                "path": "/v3/connectors",
                "data_selector": "data"
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v3/grants/{grant_id}/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "page_token",
                    "stop_condition": "not response.get('next_cursor', {})"
                }
            }
        },
        {
            "name": "contact_groups",
            "endpoint": {
                "path": "/v3/grants/{grant_id}/contacts/groups",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "page_token",
                    "stop_condition": "not response.get('next_cursor', {})"
                }
            }
        },
        {
            "name": "credentials",
            "endpoint": {
                "path": "/v3/connectors/{provider}/creds",
                "data_selector": []
            }
        },
        {
            "name": "drafts",
            "endpoint": {
                "path": "/v3/grants/{grant_id}/drafts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "page_token",
                    "stop_condition": "not response.get('next_cursor', {})"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v3/grants/{grant_id}/events",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "page_token",
                    "stop_condition": "not response.get('next_cursor', {})"
                }
            }
        },
        {
            "name": "folders",
            "endpoint": {
                "path": "/v3/grants/{grant_id}/folders",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "page_token",
                    "stop_condition": "not response.get('next_cursor', {})"
                }
            }
        },
        {
            "name": "grants",
            "endpoint": {
                "path": "/v3/grants",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "page_token",
                    "stop_condition": "not response.get('next_cursor', {})"
                }
            }
        },
        {
            "name": "messages",
            "endpoint": {
                "path": "/v3/grants/{grant_id}/messages",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "page_token",
                    "stop_condition": "not response.get('next_cursor', {})"
                }
            }
        },
        {
            "name": "scheduled_messages",
            "endpoint": {
                "path": "/v3/grants/{grant_id}/messages/schedules",
                "data_selector": "data"
            }
        },
        {
            "name": "threads",
            "endpoint": {
                "path": "/v3/grants/{grant_id}/threads",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "page_token",
                    "stop_condition": "not response.get('next_cursor', {})"
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

### Calendars
- **Endpoint Path**: `/v3/grants/{grant_id}/calendars`
- **Pagination**: Cursor-based with `page_token` as the cursor parameter and a limit of 50 records per page.
- **Data Selector**: `data`

### Connectors
- **Endpoint Path**: `/v3/connectors`
- **Data Selector**: `data`

### Contacts
- **Endpoint Path**: `/v3/grants/{grant_id}/contacts`
- **Pagination**: Cursor-based with `page_token` as the cursor parameter and a limit of 50 records per page.
- **Data Selector**: `data`

### Contact Groups
- **Endpoint Path**: `/v3/grants/{grant_id}/contacts/groups`
- **Pagination**: Cursor-based with `page_token` as the cursor parameter and a limit of 50 records per page.
- **Data Selector**: `data`

### Credentials
- **Endpoint Path**: `/v3/connectors/{provider}/creds`
- **Data Selector**: `[]`

### Drafts
- **Endpoint Path**: `/v3/grants/{grant_id}/drafts`
- **Pagination**: Cursor-based with `page_token` as the cursor parameter and a limit of 50 records per page.
- **Data Selector**: `data`

### Events
- **Endpoint Path**: `/v3/grants/{grant_id}/events`
- **Pagination**: Cursor-based with `page_token` as the cursor parameter and a limit of 50 records per page.
- **Data Selector**: `data`

### Folders
- **Endpoint Path**: `/v3/grants/{grant_id}/folders`
- **Pagination**: Cursor-based with `page_token` as the cursor parameter and a limit of 50 records per page.
- **Data Selector**: `data`

### Grants
- **Endpoint Path**: `/v3/grants`
- **Pagination**: Cursor-based with `page_token` as the cursor parameter and a limit of 50 records per page.
- **Data Selector**: `data`

### Messages
- **Endpoint Path**: `/v3/grants/{grant_id}/messages`
- **Pagination**: Cursor-based with `page_token` as the cursor parameter and a limit of 50 records per page.
- **Data Selector**: `data`

### Scheduled Messages
- **Endpoint Path**: `/v3/grants/{grant_id}/messages/schedules`
- **Data Selector**: `data`

### Threads
- **Endpoint Path**: `/v3/grants/{grant_id}/threads`
- **Pagination**: Cursor-based with `page_token` as the cursor parameter and a limit of 50 records per page.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a defined schema with fields such as `id`, `grant_id`, `data`, and others, depending on the resource type. These fields are used to map the data retrieved from the API into a structured format for further processing.