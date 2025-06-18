# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via an API token.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "shops",
            "endpoint": {
                "path": "/v1/shops.json",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page_url",
                    "stop_condition": "response.get('next_page_url') is None"
                }
            }
        },
        {
            "name": "shop_orders",
            "endpoint": {
                "path": "/v1/shops/{shop_id}/orders.json",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page_url",
                    "stop_condition": "response.get('next_page_url') is None"
                }
            }
        },
        {
            "name": "catalog_blueprints",
            "endpoint": {
                "path": "/v1/catalog/blueprints.json",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page_url",
                    "stop_condition": "response.get('next_page_url') is None"
                }
            }
        },
        {
            "name": "catalog_print_providers",
            "endpoint": {
                "path": "/v1/catalog/print_providers.json",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page_url",
                    "stop_condition": "response.get('next_page_url') is None"
                }
            }
        },
        {
            "name": "shop_products",
            "endpoint": {
                "path": "/v1/shops/{shop_id}/products.json",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page_url",
                    "stop_condition": "response.get('next_page_url') is None"
                }
            }
        },
        {
            "name": "catalog_blueprint_print_providers",
            "endpoint": {
                "path": "/v1/catalog/blueprints/{blueprint_id}/print_providers.json",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page_url",
                    "stop_condition": "response.get('next_page_url') is None"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `api_token` stored in `dlt.secrets`.

# Resources

- **Resource Name**: Abstracted from the original names
- **Endpoint Path**: Defined for each resource
- **Pagination Strategy**: Cursor-based pagination using `next_page_url`
- **Data Selector**: Typically set to "data" for extracting records

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

Each resource has a schema defined with fields, types, and nullability. Field names are generalized to maintain neutrality. For example, `id` is used as a primary key across resources.