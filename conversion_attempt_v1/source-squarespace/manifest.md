# Overview

This document provides a configuration guide for integrating with a third-party e-commerce API using dltHub's REST API source. The integration allows for seamless data synchronization from various key endpoints, such as inventory, orders, store pages, products, transactions, and profiles.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/1.0/",
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
            "name": "inventory",
            "endpoint": {
                "path": "/commerce/inventory",
                "data_selector": "inventory",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "pagination.nextPageUrl",
                    "stop_condition": "not pagination.nextPageUrl"
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/commerce/orders",
                "data_selector": "result",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "pagination.nextPageUrl",
                    "stop_condition": "not pagination.nextPageUrl"
                }
            }
        },
        {
            "name": "store_pages",
            "endpoint": {
                "path": "/commerce/store_pages",
                "data_selector": "storePages",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "pagination.nextPageUrl",
                    "stop_condition": "not pagination.nextPageUrl"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/commerce/products",
                "data_selector": "products",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "pagination.nextPageUrl",
                    "stop_condition": "not pagination.nextPageUrl"
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/commerce/transactions",
                "data_selector": "documents",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "pagination.nextPageUrl",
                    "stop_condition": "not pagination.nextPageUrl"
                }
            }
        },
        {
            "name": "profiles",
            "endpoint": {
                "path": "/profiles",
                "data_selector": "profiles",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "pagination.nextPageUrl",
                    "stop_condition": "not pagination.nextPageUrl"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Inventory
- **Endpoint Path**: `/commerce/inventory`
- **Data Selector**: `inventory`
- **Pagination**: Cursor-based using `pagination.nextPageUrl`

### Orders
- **Endpoint Path**: `/commerce/orders`
- **Data Selector**: `result`
- **Pagination**: Cursor-based using `pagination.nextPageUrl`

### Store Pages
- **Endpoint Path**: `/commerce/store_pages`
- **Data Selector**: `storePages`
- **Pagination**: Cursor-based using `pagination.nextPageUrl`

### Products
- **Endpoint Path**: `/commerce/products`
- **Data Selector**: `products`
- **Pagination**: Cursor-based using `pagination.nextPageUrl`

### Transactions
- **Endpoint Path**: `/commerce/transactions`
- **Data Selector**: `documents`
- **Pagination**: Cursor-based using `pagination.nextPageUrl`

### Profiles
- **Endpoint Path**: `/profiles`
- **Data Selector**: `profiles`
- **Pagination**: Cursor-based using `pagination.nextPageUrl`

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the data structure provided by the API. Each resource has a primary key and supports incremental synchronization based on a datetime field where applicable.