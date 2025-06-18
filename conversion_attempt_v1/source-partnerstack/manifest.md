# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, each with its own endpoint and pagination strategy. The API requires authentication and supports incremental data synchronization based on timestamps.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["public_key"],
            "password": dlt.secrets["private_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "key",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "customers",
            "endpoint": {
                "path": "/customers",
                "data_selector": ["data", "items"],
                "paginator": {
                    "type": "cursor",
                    "limit": 250,
                    "cursor_param": "starting_after",
                    "stop_condition": "response.data.has_more is false"
                }
            }
        },
        {
            "name": "deals",
            "endpoint": {
                "path": "/deals",
                "data_selector": ["data", "items"],
                "paginator": {
                    "type": "cursor",
                    "limit": 250,
                    "cursor_param": "starting_after",
                    "stop_condition": "response.data.has_more is false"
                }
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/groups",
                "data_selector": ["data", "items"],
                "paginator": {
                    "type": "cursor",
                    "limit": 250,
                    "cursor_param": "starting_after",
                    "stop_condition": "response.data.has_more is false"
                }
            }
        },
        {
            "name": "leads",
            "endpoint": {
                "path": "/leads",
                "data_selector": ["data", "items"],
                "paginator": {
                    "type": "cursor",
                    "limit": 250,
                    "cursor_param": "starting_after",
                    "stop_condition": "response.data.has_more is false"
                }
            }
        },
        {
            "name": "partnerships",
            "endpoint": {
                "path": "/partnerships",
                "data_selector": ["data", "items"],
                "paginator": {
                    "type": "cursor",
                    "limit": 250,
                    "cursor_param": "starting_after",
                    "stop_condition": "response.data.has_more is false"
                }
            }
        },
        {
            "name": "rewards",
            "endpoint": {
                "path": "/rewards",
                "data_selector": ["data", "items"],
                "paginator": {
                    "type": "cursor",
                    "limit": 250,
                    "cursor_param": "starting_after",
                    "stop_condition": "response.data.has_more is false"
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/transactions",
                "data_selector": ["data", "items"],
                "paginator": {
                    "type": "cursor",
                    "limit": 250,
                    "cursor_param": "starting_after",
                    "stop_condition": "response.data.has_more is false"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `public_key`: The public key for accessing the API.
  - `private_key`: The private key for accessing the API.

# Resources

Each resource is configured with a specific endpoint path and supports cursor-based pagination. The data is selected from a nested structure within the API response.

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Specific to each resource (e.g., `/customers`, `/deals`).
- **Pagination Strategy**: Cursor-based with a limit of 250 records per page.
- **Data Selector**: Extracts data from the `["data", "items"]` path in the response.

# Error Handling

The configuration does not explicitly define error handling strategies. It is recommended to implement retry logic for common HTTP status codes such as 429 (Too Many Requests) and 5xx server errors.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for all resources is `key`, and the data includes various fields such as `created_at`, `updated_at`, and other resource-specific attributes. The schema is flexible to accommodate null values and different data types.