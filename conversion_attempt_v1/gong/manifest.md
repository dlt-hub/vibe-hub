# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, including user data, call records, and scorecards. The API supports both full and incremental synchronization based on date-time cursors.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2/",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["access_key"],
            "password": dlt.secrets["access_key_secret"]
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
                "path": "/users",
                "data_selector": "users",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor"
                }
            }
        },
        {
            "name": "calls",
            "endpoint": {
                "path": "/calls",
                "data_selector": "calls",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "extensive_calls",
            "endpoint": {
                "path": "/calls/extensive",
                "method": "POST",
                "data_selector": "calls",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor"
                }
            }
        },
        {
            "name": "scorecards",
            "endpoint": {
                "path": "/settings/scorecards",
                "data_selector": "scorecards",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor"
                }
            }
        },
        {
            "name": "answered_scorecards",
            "endpoint": {
                "path": "/stats/activity/scorecards",
                "method": "POST",
                "data_selector": "answeredScorecards",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `access_key`: The API access key.
  - `access_key_secret`: The API access key secret.

# Resources

### Users
- **Endpoint Path**: `/users`
- **Pagination**: Cursor-based with `cursor` parameter.
- **Data Selector**: `users`

### Calls
- **Endpoint Path**: `/calls`
- **Pagination**: Cursor-based with `cursor` parameter.
- **Data Selector**: `calls`
- **Error Handling**: Retries on status codes 429, 500, 502, 503, 504 with exponential backoff.

### Extensive Calls
- **Endpoint Path**: `/calls/extensive`
- **HTTP Method**: POST
- **Pagination**: Cursor-based with `cursor` parameter.
- **Data Selector**: `calls`

### Scorecards
- **Endpoint Path**: `/settings/scorecards`
- **Pagination**: Cursor-based with `cursor` parameter.
- **Data Selector**: `scorecards`

### Answered Scorecards
- **Endpoint Path**: `/stats/activity/scorecards`
- **HTTP Method**: POST
- **Pagination**: Cursor-based with `cursor` parameter.
- **Data Selector**: `answeredScorecards`

# Error Handling

- **Retry Logic**: 
  - Retries on HTTP status codes 429, 500, 502, 503, 504.
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each resource's schema includes fields such as unique identifiers, timestamps, and other relevant data points, abstracted from the original configuration.