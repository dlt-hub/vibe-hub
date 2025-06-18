# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports OAuth2 authentication and utilizes cursor-based pagination to retrieve data from various resources.

## Configuration Example

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
            "token_url": "https://api.example.com/auth/token",
            "grant_type": "client_credentials"
        }
    },
    "resource_defaults": {
        "primary_key": "uuid",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "assignments",
            "endpoint": {
                "path": "/v1/assignments",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        },
        {
            "name": "employees",
            "endpoint": {
                "path": "/v1/employees",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        },
        {
            "name": "hashtags",
            "endpoint": {
                "path": "/v1/hash_tags",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Client ID**: Retrieved from `dlt.secrets["client_id"]`
- **Client Secret**: Retrieved from `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/auth/token`
- **Grant Type**: `client_credentials`

## Resources

### Assignments

- **Endpoint Path**: `/v1/assignments`
- **Data Selector**: `results`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `next`
  - **Stop Condition**: `not response.get('next', {})`

### Employees

- **Endpoint Path**: `/v1/employees`
- **Data Selector**: `results`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `next`
  - **Stop Condition**: `not response.get('next', {})`

### Hashtags

- **Endpoint Path**: `/v1/hash_tags`
- **Data Selector**: `results`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `next`
  - **Stop Condition**: `not response.get('next', {})`

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes such as 429, 500, 502, 503, 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with neutral field names and types. For example, the `assignments` resource includes fields such as `uuid`, `created_at`, `starts_at`, `ends_at`, and more, with appropriate data types and formats. Each field is designed to be flexible, supporting nullability where applicable.