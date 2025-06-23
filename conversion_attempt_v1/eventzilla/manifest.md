# Overview

This document provides a configuration guide for integrating with a third-party event management API. The integration allows for the extraction and synchronization of event-related data, such as attendee details, ticket sales, and event performance metrics, into your preferred data warehouses or analytics tools. This setup helps organizations centralize and analyze their event data for reporting, monitoring, and strategic insights.

# Configuration Example

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
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "events",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "attendees",
            "endpoint": {
                "path": "/v1/events/{event_id}/attendees",
                "data_selector": "attendees",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/v1/categories",
                "data_selector": "categories",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "tickets",
            "endpoint": {
                "path": "/v1/events/{event_id}/tickets",
                "data_selector": "tickets",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "users",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v1/events/{event_id}/transactions",
                "data_selector": "transactions",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`
- **Location**: Header

# Resources

- **Resource Name**: Abstracted from the original stream names (e.g., "events", "attendees")
- **Endpoint Path and Method**: Each resource has a specific path (e.g., `/v1/events`) and uses the GET method.
- **Pagination Strategy**: Offset-based pagination with parameters `limit` and `offset`.
- **Data Selector**: Specifies the field path to extract data (e.g., "events", "attendees").

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.
- **Fallback/Handling Behavior**: Use exponential backoff for retries to manage rate limits and server errors.

# Schema

- **Fields, Types, Nullability**: Each resource has a defined schema with fields such as `id`, `title`, `description`, etc.
- **Neutral Field Names**: Use generic field names like `customer_id` instead of proprietary identifiers.