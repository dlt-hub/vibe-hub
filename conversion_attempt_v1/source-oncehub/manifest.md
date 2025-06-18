# Overview

This document provides a configuration guide for integrating with a third-party scheduling API using dltHub's REST API source. The API allows for the retrieval of various resources such as bookings, event types, and user information. The integration supports incremental synchronization based on datetime fields and utilizes cursor-based pagination for efficient data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
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
            "name": "bookings",
            "endpoint": {
                "path": "/v2/bookings",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "after",
                    "stop_condition": "not 'next' in headers.get('Link')"
                }
            }
        },
        {
            "name": "booking_pages",
            "endpoint": {
                "path": "/v2/booking-pages",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "after",
                    "stop_condition": "not 'next' in headers.get('Link')"
                }
            }
        },
        {
            "name": "event_types",
            "endpoint": {
                "path": "/v2/event-types",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "after",
                    "stop_condition": "not 'next' in headers.get('Link')"
                }
            }
        },
        {
            "name": "master_pages",
            "endpoint": {
                "path": "/v2/master-pages",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "after",
                    "stop_condition": "not 'next' in headers.get('Link')"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v2/users",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "after",
                    "stop_condition": "not 'next' in headers.get('Link')"
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/v2/teams",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "after",
                    "stop_condition": "not 'next' in headers.get('Link')"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v2/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "after",
                    "stop_condition": "not 'next' in headers.get('Link')"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Configuration**: The API key is injected into the request header under the field name `API-Key`.

## Resources

### Bookings
- **Endpoint Path**: `/v2/bookings`
- **Pagination**: Cursor-based with `after` as the cursor parameter and a limit of 100 records per request.
- **Data Selector**: `data`

### Booking Pages
- **Endpoint Path**: `/v2/booking-pages`
- **Pagination**: Cursor-based with `after` as the cursor parameter and a limit of 100 records per request.
- **Data Selector**: `data`

### Event Types
- **Endpoint Path**: `/v2/event-types`
- **Pagination**: Cursor-based with `after` as the cursor parameter and a limit of 100 records per request.
- **Data Selector**: `data`

### Master Pages
- **Endpoint Path**: `/v2/master-pages`
- **Pagination**: Cursor-based with `after` as the cursor parameter and a limit of 100 records per request.
- **Data Selector**: `data`

### Users
- **Endpoint Path**: `/v2/users`
- **Pagination**: Cursor-based with `after` as the cursor parameter and a limit of 100 records per request.
- **Data Selector**: `data`

### Teams
- **Endpoint Path**: `/v2/teams`
- **Pagination**: Cursor-based with `after` as the cursor parameter and a limit of 100 records per request.
- **Data Selector**: `data`

### Contacts
- **Endpoint Path**: `/v2/contacts`
- **Pagination**: Cursor-based with `after` as the cursor parameter and a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and an exponential backoff factor of 2.

## Schema

The schema for each resource includes fields such as `id`, `name`, `object`, and other relevant attributes. All fields are defined with neutral names and types, ensuring compatibility with the dltHub format.