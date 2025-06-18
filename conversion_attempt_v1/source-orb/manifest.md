# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, including customer data, subscription details, and usage metrics. The API allows for incremental data synchronization based on timestamps and supports pagination for large datasets.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
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
            "name": "customers",
            "endpoint": {
                "path": "/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "cursor",
                    "cursor_path": "pagination_metadata.next_cursor"
                }
            }
        },
        {
            "name": "subscriptions",
            "endpoint": {
                "path": "/subscriptions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "cursor",
                    "cursor_path": "pagination_metadata.next_cursor"
                }
            }
        },
        {
            "name": "plans",
            "endpoint": {
                "path": "/plans",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "cursor",
                    "cursor_path": "pagination_metadata.next_cursor"
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/invoices",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "cursor",
                    "cursor_path": "pagination_metadata.next_cursor"
                }
            }
        },
        {
            "name": "credits_ledger_entries",
            "endpoint": {
                "path": "/customers/{customer_id}/credits/ledger",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "cursor",
                    "cursor_path": "pagination_metadata.next_cursor"
                }
            }
        },
        {
            "name": "subscription_usage",
            "endpoint": {
                "path": "/subscriptions/{subscription_id}/usage",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

## Customers
- **Endpoint Path**: `/customers`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `cursor` as the parameter and `pagination_metadata.next_cursor` as the cursor path.

## Subscriptions
- **Endpoint Path**: `/subscriptions`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `cursor` as the parameter and `pagination_metadata.next_cursor` as the cursor path.

## Plans
- **Endpoint Path**: `/plans`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `cursor` as the parameter and `pagination_metadata.next_cursor` as the cursor path.

## Invoices
- **Endpoint Path**: `/invoices`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `cursor` as the parameter and `pagination_metadata.next_cursor` as the cursor path.

## Credits Ledger Entries
- **Endpoint Path**: `/customers/{customer_id}/credits/ledger`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `cursor` as the parameter and `pagination_metadata.next_cursor` as the cursor path.

## Subscription Usage
- **Endpoint Path**: `/subscriptions/{subscription_id}/usage`
- **Primary Key**: Composite key of `subscription_id`, `billable_metric_id`, `timeframe_start`, `grouping_key`

# Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504 with a maximum of 3 retries and a backoff factor of 2.

# Schema

Each resource has a defined schema with fields such as `id`, `created_at`, and other relevant attributes. The schemas are designed to be flexible, allowing for additional properties and supporting nullability where applicable. Field names are generalized to ensure vendor neutrality.