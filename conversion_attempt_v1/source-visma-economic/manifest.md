# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports fetching data from various resources such as accounts, customers, products, and invoices. The API uses a cursor-based pagination strategy and requires API key authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["app_secret_token"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "accounts",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "collection",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPage"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "collection",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPage"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "collection",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPage"
                }
            }
        },
        {
            "name": "invoices_total",
            "endpoint": {
                "path": "/v1/invoices/totals",
                "data_selector": "collection"
            }
        }
    ]
})
```

# Authentication

The API uses API key authentication. The API key should be included in the request headers. The required secrets are:

- `app_secret_token`: The secret token for accessing the API.

# Resources

## Accounts

- **Endpoint Path**: `/v1/accounts`
- **Primary Key**: `accountNumber`
- **Pagination**: Cursor-based using `nextPage`
- **Data Selector**: `collection`

## Customers

- **Endpoint Path**: `/v1/customers`
- **Primary Key**: `customerNumber`
- **Pagination**: Cursor-based using `nextPage`
- **Data Selector**: `collection`

## Products

- **Endpoint Path**: `/v1/products`
- **Primary Key**: `productNumber`
- **Pagination**: Cursor-based using `nextPage`
- **Data Selector**: `collection`

## Invoices Total

- **Endpoint Path**: `/v1/invoices/totals`
- **Data Selector**: `collection`

# Error Handling

The API configuration includes retry logic for handling transient errors. The following settings are applied:

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined using neutral field names. For example, `accountNumber` is used instead of any branded term. The schema includes field types, descriptions, and constraints such as `filterable` and `sortable` attributes.