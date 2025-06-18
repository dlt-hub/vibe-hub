# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as transactions, customers, invoices, disputes, refunds, settlements, subscriptions, and transfers. The API uses bearer token authentication and supports pagination and incremental synchronization based on datetime fields.

## Configuration Example

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
            "name": "transactions",
            "endpoint": {
                "path": "/v1/transaction",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 200
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customer",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 200
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/v1/paymentrequest",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 200
                }
            }
        },
        {
            "name": "disputes",
            "endpoint": {
                "path": "/v1/dispute",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 200
                }
            }
        },
        {
            "name": "refunds",
            "endpoint": {
                "path": "/v1/refund",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 200
                }
            }
        },
        {
            "name": "settlements",
            "endpoint": {
                "path": "/v1/settlement",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 200
                }
            }
        },
        {
            "name": "subscriptions",
            "endpoint": {
                "path": "/v1/subscription",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 200
                }
            }
        },
        {
            "name": "transfers",
            "endpoint": {
                "path": "/v1/transfer",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 200
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_token"]`

## Resources

### Transactions
- **Endpoint Path**: `/v1/transaction`
- **Pagination**: Page number with `page` and `perPage` parameters
- **Data Selector**: `data`

### Customers
- **Endpoint Path**: `/v1/customer`
- **Pagination**: Page number with `page` and `perPage` parameters
- **Data Selector**: `data`

### Invoices
- **Endpoint Path**: `/v1/paymentrequest`
- **Pagination**: Page number with `page` and `perPage` parameters
- **Data Selector**: `data`

### Disputes
- **Endpoint Path**: `/v1/dispute`
- **Pagination**: Page number with `page` and `perPage` parameters
- **Data Selector**: `data`

### Refunds
- **Endpoint Path**: `/v1/refund`
- **Pagination**: Page number with `page` and `perPage` parameters
- **Data Selector**: `data`

### Settlements
- **Endpoint Path**: `/v1/settlement`
- **Pagination**: Page number with `page` and `perPage` parameters
- **Data Selector**: `data`

### Subscriptions
- **Endpoint Path**: `/v1/subscription`
- **Pagination**: Page number with `page` and `perPage` parameters
- **Data Selector**: `data`

### Transfers
- **Endpoint Path**: `/v1/transfer`
- **Pagination**: Page number with `page` and `perPage` parameters
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource includes fields such as `id`, `createdAt`, `amount`, `currency`, and other relevant fields. All fields are nullable and support various data types like integer, string, boolean, and object.