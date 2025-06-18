# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API uses OAuth2 authentication for secure access.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/auth/token"
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
                "path": "/v1/users",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "links.next",
                    "stop_condition": "not response.get('links', {}).get('next')"
                }
            }
        },
        {
            "name": "user_groups",
            "endpoint": {
                "path": "/v1/user-groups",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "links.next",
                    "stop_condition": "not response.get('links', {}).get('next')"
                }
            }
        },
        {
            "name": "tracker_settings",
            "endpoint": {
                "path": "/v1/tracker/settings",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "links.next",
                    "stop_condition": "not response.get('links', {}).get('next')"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: `dlt.secrets["client_id"]`
- **Client Secret**: `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/auth/token`

# Resources

### Users
- **Endpoint Path**: `/v1/users`
- **Data Selector**: `data`
- **Pagination Strategy**: Cursor-based
  - **Cursor Path**: `links.next`
  - **Stop Condition**: `not response.get('links', {}).get('next')`

### User Groups
- **Endpoint Path**: `/v1/user-groups`
- **Data Selector**: `data`
- **Pagination Strategy**: Cursor-based
  - **Cursor Path**: `links.next`
  - **Stop Condition**: `not response.get('links', {}).get('next')`

### Tracker Settings
- **Endpoint Path**: `/v1/tracker/settings`
- **Data Selector**: `data`
- **Pagination Strategy**: Cursor-based
  - **Cursor Path**: `links.next`
  - **Stop Condition**: `not response.get('links', {}).get('next')`

# Error Handling

- **Retry Logic**: 
  - **Max Retries**: 3
  - **Backoff Strategy**: Exponential with a factor of 2
- **HTTP Status Codes**: 
  - **Retry On**: 429 (Rate Limit Exceeded)
- **Error Message**: "Rate limits hit"

# Schema

The schema for each resource is defined with neutral field names and types. For example, the `users` resource includes fields like `id`, `email`, and `language`, all of which are strings and may be nullable.