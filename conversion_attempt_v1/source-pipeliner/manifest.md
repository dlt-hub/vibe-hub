# Overview

This document provides a configuration example for integrating with a third-party CRM tool's REST API using dltHub's REST API source format. The integration allows fetching data from various resources such as accounts, activities, clients, contacts, and more. The API supports cursor-based pagination and requires basic authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/v100/rest/spaces/{space_id}/",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge",
        "endpoint": {
            "params": {
                "first": 100
            }
        }
    },
    "resources": [
        {
            "name": "accounts",
            "endpoint": {
                "path": "entities/Accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "response.page_info.end_cursor",
                    "stop_condition": "not response.page_info.has_next_page"
                }
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "entities/Activities",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "response.page_info.end_cursor",
                    "stop_condition": "not response.page_info.has_next_page"
                }
            }
        },
        {
            "name": "clients",
            "endpoint": {
                "path": "entities/Clients",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "response.page_info.end_cursor",
                    "stop_condition": "not response.page_info.has_next_page"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "entities/Contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "response.page_info.end_cursor",
                    "stop_condition": "not response.page_info.has_next_page"
                }
            }
        },
        {
            "name": "entities",
            "endpoint": {
                "path": "entities/Entities",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "response.page_info.end_cursor",
                    "stop_condition": "not response.page_info.has_next_page"
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: The username for API access.
  - `password`: The password for API access.

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: A neutral name representing the data being fetched.
- **Endpoint Path**: The API path to access the resource.
- **HTTP Method**: GET (default).
- **Data Selector**: The JSON path to extract data from the API response.
- **Pagination Strategy**: Cursor-based pagination using parameters `after` and `first`.

# Error Handling

The configuration does not explicitly define error handling logic. However, typical error handling in dltHub can include:

- **Retry Logic**: Retry on specific HTTP status codes (e.g., 429, 500).
- **Backoff Strategy**: Exponential backoff for retries.
- **Timeouts**: Handling for request timeouts or data limits.

# Schema

The schema for each resource is defined with neutral field names and types. Each resource has a primary key field named `id`. The schema is flexible to accommodate various data types, including strings, numbers, and booleans, with support for nullable fields.