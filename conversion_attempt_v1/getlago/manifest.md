# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as metrics, plans, coupons, and more, each with its own endpoint and pagination strategy.

## Configuration Example

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
            "name": "metrics",
            "endpoint": {
                "path": "/v1/metrics",
                "data_selector": "metrics",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page",
                    "cursor_path": "response.meta.next_page"
                }
            }
        },
        {
            "name": "plans",
            "endpoint": {
                "path": "/v1/plans",
                "data_selector": "plans",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page",
                    "cursor_path": "response.meta.next_page"
                }
            }
        },
        {
            "name": "coupons",
            "endpoint": {
                "path": "/v1/coupons",
                "data_selector": "coupons",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page",
                    "cursor_path": "response.meta.next_page"
                }
            }
        },
        {
            "name": "add_ons",
            "endpoint": {
                "path": "/v1/add_ons",
                "data_selector": "add_ons",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page",
                    "cursor_path": "response.meta.next_page"
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/v1/invoices",
                "data_selector": "invoices",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page",
                    "cursor_path": "response.meta.next_page"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "customers",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page",
                    "cursor_path": "response.meta.next_page"
                }
            }
        },
        {
            "name": "subscriptions",
            "endpoint": {
                "path": "/v1/subscriptions",
                "data_selector": "subscriptions",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page",
                    "cursor_path": "response.meta.next_page"
                }
            }
        },
        {
            "name": "wallets",
            "endpoint": {
                "path": "/v1/wallets",
                "data_selector": "wallets",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page",
                    "cursor_path": "response.meta.next_page"
                }
            }
        },
        {
            "name": "customer_usage",
            "endpoint": {
                "path": "/v1/customer_usage",
                "data_selector": "customer_usage",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page",
                    "cursor_path": "response.meta.next_page"
                }
            }
        },
        {
            "name": "fees",
            "endpoint": {
                "path": "/v1/fees",
                "data_selector": "fees",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page",
                    "cursor_path": "response.meta.next_page"
                }
            }
        },
        {
            "name": "customer_usage_past",
            "endpoint": {
                "path": "/v1/customer_usage_past",
                "data_selector": "usage_periods",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page",
                    "cursor_path": "response.meta.next_page"
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

Each resource is defined with a unique name, endpoint path, data selector, and pagination strategy. The pagination strategy uses a cursor-based approach with a limit of 100 records per page.

## Error Handling

The configuration includes error handling for HTTP status codes and retry logic. Specific error handling strategies can be implemented as needed.

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with various data structures. Each resource has a primary key for data integrity.