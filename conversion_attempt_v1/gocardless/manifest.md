# Overview

This document provides a configuration example for integrating with a third-party payment processing API using the dltHub REST API source. The integration supports retrieving data from various resources such as payments, refunds, mandates, and payouts. The API uses bearer token authentication and supports cursor-based pagination.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["access_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "payments",
            "endpoint": {
                "path": "/v1/payments",
                "data_selector": "payments",
                "paginator": {
                    "type": "cursor",
                    "limit": 500,
                    "cursor_param": "after",
                    "cursor_path": "meta.cursors.after"
                }
            }
        },
        {
            "name": "refunds",
            "endpoint": {
                "path": "/v1/refunds",
                "data_selector": "refunds",
                "paginator": {
                    "type": "cursor",
                    "limit": 500,
                    "cursor_param": "after",
                    "cursor_path": "meta.cursors.after"
                }
            }
        },
        {
            "name": "mandates",
            "endpoint": {
                "path": "/v1/mandates",
                "data_selector": "mandates",
                "paginator": {
                    "type": "cursor",
                    "limit": 500,
                    "cursor_param": "after",
                    "cursor_path": "meta.cursors.after"
                }
            }
        },
        {
            "name": "payouts",
            "endpoint": {
                "path": "/v1/payouts",
                "data_selector": "payouts",
                "paginator": {
                    "type": "cursor",
                    "limit": 500,
                    "cursor_param": "after",
                    "cursor_path": "meta.cursors.after"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["access_token"]`

# Resources

## Payments

- **Endpoint Path**: `/v1/payments`
- **Data Selector**: `payments`
- **Pagination**: Cursor-based with `after` parameter and a limit of 500

## Refunds

- **Endpoint Path**: `/v1/refunds`
- **Data Selector**: `refunds`
- **Pagination**: Cursor-based with `after` parameter and a limit of 500

## Mandates

- **Endpoint Path**: `/v1/mandates`
- **Data Selector**: `mandates`
- **Pagination**: Cursor-based with `after` parameter and a limit of 500

## Payouts

- **Endpoint Path**: `/v1/payouts`
- **Data Selector**: `payouts`
- **Pagination**: Cursor-based with `after` parameter and a limit of 500

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a maximum of 3 retries and a backoff factor of 2.

# Schema

The schema for each resource includes fields such as `id`, `created_at`, `amount`, `currency`, and other relevant fields. All fields are nullable and follow a neutral naming convention.