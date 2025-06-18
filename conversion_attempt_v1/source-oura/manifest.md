# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with specific endpoints and pagination strategies. The API requires authentication via an API key, and data is retrieved using GET requests.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2/usercollection",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "timestamp",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "daily_activity",
            "endpoint": {
                "path": "/daily_activity",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_token"
                }
            }
        },
        {
            "name": "daily_readiness",
            "endpoint": {
                "path": "/daily_readiness",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_token"
                }
            }
        },
        {
            "name": "daily_sleep",
            "endpoint": {
                "path": "/daily_sleep",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_token"
                }
            }
        },
        {
            "name": "heart_rate",
            "endpoint": {
                "path": "/heartrate",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_token"
                }
            }
        },
        {
            "name": "sessions",
            "endpoint": {
                "path": "/session",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_token"
                }
            }
        },
        {
            "name": "sleep_periods",
            "endpoint": {
                "path": "/sleep",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_token"
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/tag",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_token"
                }
            }
        },
        {
            "name": "workouts",
            "endpoint": {
                "path": "/workout",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_token"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

Each resource corresponds to a specific endpoint and uses cursor-based pagination. The data is selected from the "data" field in the response.

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Specific to each resource
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based using the "next_token" parameter
- **Data Selector**: "data"

# Error Handling

The configuration does not explicitly define error handling strategies. However, typical practices include retrying on specific HTTP status codes (e.g., 429, 500) with exponential backoff.

# Schema

The schema for each resource is defined using JSON Schema. Field names are generalized, and types are specified as either string, number, or null. The primary key for most resources is "timestamp," except where specified otherwise.

- **Fields**: Generalized field names (e.g., "score", "timestamp")
- **Types**: String, number, or null
- **Primary Key**: Typically "timestamp" or as specified per resource

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source.