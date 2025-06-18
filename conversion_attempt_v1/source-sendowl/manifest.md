# Overview

This document provides a configuration guide for integrating with a third-party digital commerce platform's REST API. The integration allows for data extraction from various resources such as products, packages, orders, and subscriptions. The API supports pagination and requires basic authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "products",
            "endpoint": {
                "path": "/products",
                "data_selector": "product",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 50
                }
            }
        },
        {
            "name": "packages",
            "endpoint": {
                "path": "/packages",
                "data_selector": "package",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 50
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/orders",
                "data_selector": "order",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 50
                }
            },
            "incremental_sync": {
                "cursor_field": "created_at",
                "start_datetime": dlt.config["start_date"],
                "end_datetime": "now"
            }
        },
        {
            "name": "subscriptions",
            "endpoint": {
                "path": "/subscriptions",
                "data_selector": "subscription",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 50
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: API Key
  - `password`: API Secret

# Resources

## Products
- **Endpoint Path**: `/products`
- **Data Selector**: `product`
- **Pagination**: Page number with parameters `page` and `per_page`, limit 50

## Packages
- **Endpoint Path**: `/packages`
- **Data Selector**: `package`
- **Pagination**: Page number with parameters `page` and `per_page`, limit 50

## Orders
- **Endpoint Path**: `/orders`
- **Data Selector**: `order`
- **Pagination**: Page number with parameters `page` and `per_page`, limit 50
- **Incremental Sync**: Based on `created_at` field

## Subscriptions
- **Endpoint Path**: `/subscriptions`
- **Data Selector**: `subscription`
- **Pagination**: Page number with parameters `page` and `per_page`, limit 50

# Error Handling

- **Retry Logic**: Implement retry on common HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a maximum of 3 retries.

# Schema

Each resource has a defined schema with fields such as `id`, `created_at`, and other relevant attributes. The schemas are flexible to accommodate null values and various data types.