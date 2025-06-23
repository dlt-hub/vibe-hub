# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and configuration. The API uses a bearer token for authentication and supports cursor-based pagination for data retrieval.

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
                "data_selector": "_results",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('_pagination', {}).get('next', {}) }}",
                    "stop_condition": "{{ not response.get('_pagination', {}).get('next') }}"
                }
            }
        },
        {
            "name": "inboxes",
            "endpoint": {
                "path": "/v1/inboxes",
                "data_selector": "_results",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('_pagination', {}).get('next', {}) }}",
                    "stop_condition": "{{ not response.get('_pagination', {}).get('next') }}"
                }
            }
        },
        {
            "name": "accounts",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "_results",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('_pagination', {}).get('next', {}) }}",
                    "stop_condition": "{{ not response.get('_pagination', {}).get('next') }}"
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

### Events
- **Endpoint Path**: `/v1/events`
- **Pagination**: Cursor-based
- **Data Selector**: `_results`

### Inboxes
- **Endpoint Path**: `/v1/inboxes`
- **Pagination**: Cursor-based
- **Data Selector**: `_results`

### Accounts
- **Endpoint Path**: `/v1/accounts`
- **Pagination**: Cursor-based
- **Data Selector**: `_results`

# Error Handling

- **Retry Logic**: 
  - Max Retries: 3
  - Backoff Strategy: Exponential with a factor of 2
- **HTTP Status Codes**: Retries on 429 (Rate Limit) and other server errors (500, 502, 503, 504)

# Schema

Each resource has a schema defined with fields such as `id`, `created_at`, and `updated_at`. The schemas are designed to be flexible, allowing for additional properties as needed. Field names are generalized to ensure vendor neutrality.