# Overview

This document provides a configuration example for integrating with a third-party calendar and event management API using dltHub's REST API source format. The integration allows for the extraction of event and guest data, supporting pagination and authentication via API key.

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
        "primary_key": "api_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/calendar/list-events",
                "data_selector": "entries.*.event",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination_cursor",
                    "cursor_path": "next_cursor",
                    "stop_condition": "has_more == false"
                }
            }
        },
        {
            "name": "event_guests",
            "endpoint": {
                "path": "/v1/event/get-guests",
                "data_selector": "entries.*.guest",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination_cursor",
                    "cursor_path": "next_cursor",
                    "stop_condition": "has_more == false"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Configuration**: The API key is injected into the request header as `x-luma-api-key`.

## Resources

### Events

- **Endpoint Path**: `/v1/calendar/list-events`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based pagination using `pagination_cursor` as the parameter and `next_cursor` as the cursor path. The pagination stops when `has_more` is false.
- **Data Selector**: `entries.*.event`

### Event Guests

- **Endpoint Path**: `/v1/event/get-guests`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based pagination using `pagination_cursor` as the parameter and `next_cursor` as the cursor path. The pagination stops when `has_more` is false.
- **Data Selector**: `entries.*.guest`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

### Events

- **Fields**:
  - `api_id`: string
  - `description`: string or null
  - `cover_url`: string or null
  - `created_at`: string or null
  - `duration_interval`: string or null
  - `end_at`: string or null
  - `event_type`: string or null
  - `geo_address_json`: object or null
  - `geo_latitude`: string or null
  - `geo_longitude`: string or null
  - `meeting_url`: string or null
  - `name`: string or null
  - `start_at`: string or null
  - `timezone`: string or null
  - `url`: string or null
  - `user_api_id`: string or null
  - `visibility`: string or null
  - `zoom_meeting_url`: string or null

### Event Guests

- **Fields**:
  - `api_id`: string
  - `approval_status`: string or null
  - `check_in_qr_code`: string or null
  - `created_at`: string or null
  - `custom_source`: string or null
  - `email`: string or null
  - `eth_address`: string or null
  - `event_ticket`: object or null
  - `event_tickets`: array or null
  - `invited_at`: string or null
  - `name`: string or null
  - `phone_number`: string or null
  - `registered_at`: string or null
  - `registration_answers`: array or null
  - `user_api_id`: string or null
  - `user_email`: string or null
  - `user_name`: string or null

This configuration provides a clean and generalized setup for integrating with a third-party API, ensuring compatibility with dltHub's REST API source format.