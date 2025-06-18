# Overview

This document provides a configuration guide for integrating with a third-party event management API using dltHub's REST API source. The integration allows for seamless data synchronization, enabling the extraction and integration of data related to events, attendees, and tickets into your data warehouse. This setup automates the flow of information, providing up-to-date insights on event registrations, ticketing, and attendee information.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["session_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "events",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "attendees",
            "endpoint": {
                "path": "/v1/attendees",
                "data_selector": "attendees",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "since",
                    "cursor_path": "attendees[-1].ticket_code",
                    "stop_condition": "not response.get('attendees')"
                }
            }
        },
        {
            "name": "tickets",
            "endpoint": {
                "path": "/v1/tickets",
                "data_selector": "tickets",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Secrets**: 
  - `session_token`: Retrieved via a login process using email and password credentials.

## Resources

### Events
- **Endpoint Path**: `/v1/events`
- **HTTP Method**: POST
- **Data Selector**: `events`
- **Primary Key**: `id`
- **Pagination**: Single page (no pagination)

### Attendees
- **Endpoint Path**: `/v1/attendees`
- **HTTP Method**: POST
- **Data Selector**: `attendees`
- **Primary Key**: `id`
- **Pagination**: Cursor-based
  - **Cursor Parameter**: `since`
  - **Cursor Path**: `attendees[-1].ticket_code`
  - **Stop Condition**: `not response.get('attendees')`

### Tickets
- **Endpoint Path**: `/v1/tickets`
- **HTTP Method**: POST
- **Data Selector**: `tickets`
- **Primary Key**: `code`
- **Pagination**: Single page (no pagination)

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP errors such as 429, 500, 502, 503, and 504 with retries.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 for retries.

## Schema

### Events
- **Fields**:
  - `id`: number
  - `name`: string or null
  - `location`: string or null
  - `starts_at`: string or null
  - `ticket_types`: array or null
  - `tickets`: number or null
  - `tickets_checked_in`: number or null
  - `tickets_sold`: number or null

### Attendees
- **Fields**:
  - `id`: string
  - `created_at`: string or null
  - `ticket_code`: string or null
  - `ticket_id`: string or null
  - `ticket_type`: object or null
  - `values`: array or null

### Tickets
- **Fields**:
  - `code`: string
  - `checked_in`: boolean or null
  - `checkins`: array or null
  - `ticket_type_id`: number or null

This configuration provides a clean and vendor-neutral setup for integrating with a third-party event management API using dltHub's REST API source.