# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via a bearer token.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "sessions",
            "endpoint": {
                "path": "/v2/sessions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "last",
                    "stop_condition": "response.get('last') is None"
                }
            }
        },
        {
            "name": "rooms",
            "endpoint": {
                "path": "/v2/rooms",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "last",
                    "stop_condition": "response.get('last') is None"
                }
            }
        },
        {
            "name": "active_room_peers",
            "endpoint": {
                "path": "/v2/active-rooms/{room_id}/peers",
                "data_selector": "peers",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "last",
                    "stop_condition": "response.get('last') is None"
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/v2/templates",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "last",
                    "stop_condition": "response.get('last') is None"
                }
            }
        },
        {
            "name": "template_settings",
            "endpoint": {
                "path": "/v2/templates/{temp_id}/settings",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "last",
                    "stop_condition": "response.get('last') is None"
                }
            }
        },
        {
            "name": "templates_destinations",
            "endpoint": {
                "path": "/v2/templates/{temp_id}/destinations",
                "data_selector": "browserRecordings",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "last",
                    "stop_condition": "response.get('last') is None"
                }
            }
        },
        {
            "name": "analytics_events",
            "endpoint": {
                "path": "/v2/analytics/events",
                "data_selector": "events",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "next",
                    "stop_condition": "response.get('next') is None"
                }
            }
        },
        {
            "name": "recordings",
            "endpoint": {
                "path": "/v2/recordings",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "last",
                    "stop_condition": "response.get('last') is None"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: `dlt.secrets["api_token"]`

# Resources

- **sessions**: Fetches session data from the API.
  - **Endpoint Path**: `/v2/sessions`
  - **Pagination**: Cursor-based using the `start` parameter and `last` cursor path.

- **rooms**: Retrieves room information.
  - **Endpoint Path**: `/v2/rooms`
  - **Pagination**: Cursor-based using the `start` parameter and `last` cursor path.

- **active_room_peers**: Accesses active room peer data.
  - **Endpoint Path**: `/v2/active-rooms/{room_id}/peers`
  - **Pagination**: Cursor-based using the `start` parameter and `last` cursor path.

- **templates**: Obtains template details.
  - **Endpoint Path**: `/v2/templates`
  - **Pagination**: Cursor-based using the `start` parameter and `last` cursor path.

- **template_settings**: Fetches settings for templates.
  - **Endpoint Path**: `/v2/templates/{temp_id}/settings`
  - **Pagination**: Cursor-based using the `start` parameter and `last` cursor path.

- **templates_destinations**: Retrieves template destination data.
  - **Endpoint Path**: `/v2/templates/{temp_id}/destinations`
  - **Pagination**: Cursor-based using the `start` parameter and `last` cursor path.

- **analytics_events**: Accesses analytics event data.
  - **Endpoint Path**: `/v2/analytics/events`
  - **Pagination**: Cursor-based using the `start` parameter and `next` cursor path.

- **recordings**: Fetches recording information.
  - **Endpoint Path**: `/v2/recordings`
  - **Pagination**: Cursor-based using the `start` parameter and `last` cursor path.

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504) with a maximum of 3 retries and a backoff factor of 2.

# Schema

- **sessions**: Includes fields like `id`, `active`, `created_at`, `customer_id`, etc.
- **rooms**: Contains fields such as `id`, `name`, `created_at`, `updated_at`, etc.
- **active_room_peers**: Features fields like `uuid`, `metadata`, `joined_at`, etc.
- **templates**: Comprises fields such as `id`, `name`, `createdAt`, etc.
- **template_settings**: Includes fields like `uuid`, `region`, `retry`, etc.
- **templates_destinations**: Contains fields such as `uuid`, `autoStopTimeout`, `height`, etc.
- **analytics_events**: Features fields like `id`, `version`, `type`, `data`, etc.
- **recordings**: Comprises fields such as `id`, `created_at`, `destination`, etc.