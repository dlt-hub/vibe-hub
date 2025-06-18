# Overview

This document provides a configuration example for integrating with a third-party scheduling API using dltHub's REST API source. The integration allows you to sync scheduled events, scheduling links, and time zone data. The API supports cursor-based pagination and requires authentication via an API key.

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
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "entries",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "metadata.after",
                    "stop_condition": "metadata.after is none"
                }
            }
        },
        {
            "name": "scheduling_links",
            "endpoint": {
                "path": "/v1/links",
                "data_selector": "entries",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "metadata.after",
                    "stop_condition": "metadata.after is none"
                }
            }
        },
        {
            "name": "timezones",
            "endpoint": {
                "path": "/v1/time_zones",
                "data_selector": []
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `api_key` stored in dlt's secrets configuration.

# Resources

## Events
- **Endpoint Path**: `/v1/events`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `after`
  - **Cursor Path**: `metadata.after`
  - **Stop Condition**: `metadata.after is none`
- **Data Selector**: `entries`

## Scheduling Links
- **Endpoint Path**: `/v1/links`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `after`
  - **Cursor Path**: `metadata.after`
  - **Stop Condition**: `metadata.after is none`
- **Data Selector**: `entries`

## Timezones
- **Endpoint Path**: `/v1/time_zones`
- **Data Selector**: `[]`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Events**: Contains fields related to scheduled events.
- **Scheduling Links**: Contains fields related to scheduling links.
- **Timezones**: Contains fields related to time zone data.

Note: The schema fields are generalized and do not include specific properties.