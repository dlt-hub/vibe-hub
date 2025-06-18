# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data retrieval operations, including transactions, balances, product listings, disputes, invoices, and payments. The API supports both incremental and full data syncs, with pagination and error handling mechanisms.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/v1/oauth2/token",
            "grant_type": "client_credentials"
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
                "path": "/v1/reporting/transactions",
                "data_selector": "transaction_details",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 500
                }
            }
        },
        {
            "name": "balances",
            "endpoint": {
                "path": "/v1/reporting/balances",
                "data_selector": [],
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "list_products",
            "endpoint": {
                "path": "/v1/catalogs/products",
                "data_selector": "products",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 20
                }
            }
        },
        {
            "name": "show_product_details",
            "endpoint": {
                "path": "/v1/catalogs/products/{{ stream_slice.id }}",
                "data_selector": [],
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "list_disputes",
            "endpoint": {
                "path": "/v1/customer/disputes",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "next_page_token",
                    "limit_param": "page_size",
                    "limit": 50
                }
            }
        },
        {
            "name": "search_invoices",
            "endpoint": {
                "path": "/v2/invoicing/search-invoices",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "list_payments",
            "endpoint": {
                "path": "/v1/payments/payment",
                "data_selector": "payments",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start_id",
                    "limit_param": "count",
                    "limit": 20
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: `dlt.secrets["client_id"]`
- **Client Secret**: `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/v1/oauth2/token`
- **Grant Type**: `client_credentials`

# Resources

### Transactions
- **Endpoint Path**: `/v1/reporting/transactions`
- **Data Selector**: `transaction_details`
- **Pagination**: Page number with `page` and `page_size` parameters, limit 500

### Balances
- **Endpoint Path**: `/v1/reporting/balances`
- **Data Selector**: None
- **Pagination**: Single page

### List Products
- **Endpoint Path**: `/v1/catalogs/products`
- **Data Selector**: `products`
- **Pagination**: Page number with `page` and `page_size` parameters, limit 20

### Show Product Details
- **Endpoint Path**: `/v1/catalogs/products/{{ stream_slice.id }}`
- **Data Selector**: None
- **Pagination**: Single page

### List Disputes
- **Endpoint Path**: `/v1/customer/disputes`
- **Data Selector**: `items`
- **Pagination**: Page number with `next_page_token` and `page_size` parameters, limit 50

### Search Invoices
- **Endpoint Path**: `/v2/invoicing/search-invoices`
- **Data Selector**: `items`
- **Pagination**: Page number with `page` and `page_size` parameters, limit 100

### List Payments
- **Endpoint Path**: `/v1/payments/payment`
- **Data Selector**: `payments`
- **Pagination**: Cursor with `start_id` and `count` parameters, limit 20

# Error Handling

- **Retry on Status Codes**: [400, 429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined in a neutral format, with fields such as `transaction_id`, `as_of_time`, `id`, and `dispute_id` serving as primary keys. Each field is described with its type and constraints, ensuring compatibility with the API's data structure.