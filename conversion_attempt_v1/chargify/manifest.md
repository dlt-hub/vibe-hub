# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization for various resources such as customers, subscriptions, invoices, coupons, and transactions. The configuration is designed to be vendor-neutral and follows dltHub's REST API source format.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": "x"
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
                "path": "/customers.json",
                "data_selector": "customer",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "subscriptions",
            "endpoint": {
                "path": "/subscriptions.json",
                "data_selector": "subscription",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/invoices",
                "data_selector": "invoices.json",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "coupons",
            "endpoint": {
                "path": "/coupons.json",
                "data_selector": "coupon",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/transaction",
                "data_selector": "transactions.json",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: Used as the username for authentication
  - Password is set to a static value "x"

# Resources

- **Customers**
  - **Endpoint Path**: `/customers.json`
  - **Data Selector**: `customer`
  - **Pagination**: Page number with parameters `page` and `per_page`, limit 200

- **Subscriptions**
  - **Endpoint Path**: `/subscriptions.json`
  - **Data Selector**: `subscription`
  - **Pagination**: Page number with parameters `page` and `per_page`, limit 200

- **Invoices**
  - **Endpoint Path**: `/invoices`
  - **Data Selector**: `invoices.json`
  - **Pagination**: Page number with parameters `page` and `per_page`, limit 200

- **Coupons**
  - **Endpoint Path**: `/coupons.json`
  - **Data Selector**: `coupon`
  - **Pagination**: Page number with parameters `page` and `per_page`, limit 200

- **Transactions**
  - **Endpoint Path**: `/transaction`
  - **Data Selector**: `transactions.json`
  - **Pagination**: Page number with parameters `page` and `per_page`, limit 200

# Error Handling

- **Retry Logic**: Not explicitly defined in the YAML, but can be configured in dltHub with retry_on_status_codes and backoff_factor if needed.
- **HTTP Status Codes**: Common retry status codes include 429, 500, 502, 503, 504.

# Schema

- **Customers**: Includes fields like `id`, `email`, `first_name`, `last_name`, etc.
- **Subscriptions**: Includes fields like `id`, `customer_id`, `created_at`, etc.
- **Invoices**: Includes fields like `uid`, `customer_id`, `issue_date`, etc.
- **Coupons**: Includes fields like `id`, `code`, `amount`, etc.
- **Transactions**: Includes fields like `id`, `amount_in_cents`, `customer_id`, etc.

Each schema is defined with neutral field names and types, ensuring compatibility with various data structures.