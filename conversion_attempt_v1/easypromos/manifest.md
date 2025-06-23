# Overview

This document provides a configuration guide for integrating with a third-party API that facilitates data extraction related to promotions, brands, stages, users, participations, prizes, and rankings. The integration supports automatic syncing of various data points into data warehouses, enabling streamlined analytics and reporting. This setup helps businesses analyze campaign data and optimize marketing strategies.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["bearer_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "promotions",
            "endpoint": {
                "path": "/promotions",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_cursor",
                    "cursor_path": "paging.next_cursor"
                }
            }
        },
        {
            "name": "organizing_brands",
            "endpoint": {
                "path": "/organizing_brands",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_cursor",
                    "cursor_path": "paging.next_cursor"
                }
            }
        },
        {
            "name": "stages",
            "endpoint": {
                "path": "/stages/{promotion_id}",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_cursor",
                    "cursor_path": "paging.next_cursor"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users/{promotion_id}",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_cursor",
                    "cursor_path": "paging.next_cursor"
                }
            }
        },
        {
            "name": "participations",
            "endpoint": {
                "path": "/participations/{promotion_id}",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_cursor",
                    "cursor_path": "paging.next_cursor"
                }
            }
        },
        {
            "name": "prizes",
            "endpoint": {
                "path": "/prizes/{promotion_id}",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_cursor",
                    "cursor_path": "paging.next_cursor"
                }
            }
        },
        {
            "name": "rankings",
            "endpoint": {
                "path": "/ranking/{promotion_id}",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_cursor",
                    "cursor_path": "paging.next_cursor"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Stored securely in `dlt.secrets["bearer_token"]`

# Resources

- **Resource Name**: Abstracted from the original context
- **Endpoint Path**: Defined for each resource, with placeholders for dynamic segments
- **Pagination Strategy**: Cursor-based pagination using `next_cursor` parameter
- **Data Selector**: Extracts data from the `items` field

# Error Handling

- **Retry Logic**: Implement retry mechanisms for handling transient errors
- **HTTP Status Codes**: Handle common HTTP errors such as 429, 500, 502, 503, and 504
- **Backoff Strategy**: Exponential backoff for retries

# Schema

- **Fields**: Defined per resource with neutral field names
- **Types**: Consistent with the original schema, using generalized field names
- **Nullability**: Fields may be nullable, as indicated in the schema

This configuration provides a clean, vendor-neutral setup for integrating with a third-party API, ensuring seamless data extraction and synchronization.