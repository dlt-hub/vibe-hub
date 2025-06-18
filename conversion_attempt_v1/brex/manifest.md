# Overview

This document provides a configuration guide for integrating with a third-party financial management API using dltHub's REST API source. The integration allows fetching data on users, expenses, transactions, vendors, and budgets. The API supports incremental synchronization based on date fields and uses cursor-based pagination for data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["user_token"]
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
                "path": "/v2/transactions/card/primary",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v2/users",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "departments",
            "endpoint": {
                "path": "/v2/departments",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "vendors",
            "endpoint": {
                "path": "/v1/vendors",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "expenses",
            "endpoint": {
                "path": "/v1/expenses/card",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "budgets",
            "endpoint": {
                "path": "/v2/budgets",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "next_cursor"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer Token
- **Token**: Use the `user_token` stored in dlt secrets for authentication.

## Resources

### Transactions
- **Endpoint Path**: `/v2/transactions/card/primary`
- **Primary Key**: `id`
- **Data Selector**: `items`
- **Pagination**: Cursor-based using `cursor` parameter and `next_cursor` path.

### Users
- **Endpoint Path**: `/v2/users`
- **Primary Key**: `id`
- **Data Selector**: `items`
- **Pagination**: Cursor-based using `cursor` parameter and `next_cursor` path.

### Departments
- **Endpoint Path**: `/v2/departments`
- **Primary Key**: `id`
- **Data Selector**: `items`
- **Pagination**: Cursor-based using `cursor` parameter and `next_cursor` path.

### Vendors
- **Endpoint Path**: `/v1/vendors`
- **Primary Key**: `id`
- **Data Selector**: `items`
- **Pagination**: Cursor-based using `cursor` parameter and `next_cursor` path.

### Expenses
- **Endpoint Path**: `/v1/expenses/card`
- **Primary Key**: `id`
- **Data Selector**: `items`
- **Pagination**: Cursor-based using `cursor` parameter and `next_cursor` path.

### Budgets
- **Endpoint Path**: `/v2/budgets`
- **Primary Key**: `budget_id`
- **Data Selector**: `items`
- **Pagination**: Cursor-based using `cursor` parameter and `next_cursor` path.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Transactions
- **Fields**: 
  - `id` (string, required)
  - `posted_at_date` (string, required)
  - `type` (string or null)
  - `description` (string or null)
  - `amount` (object or null)
    - `amount` (number or null)
    - `currency` (string or null)
  - `card_id` (string or null)
  - `initiated_at_date` (string or null)
  - `merchant` (object or null)
    - `country` (string or null)
    - `mcc` (string or null)
    - `raw_descriptor` (string or null)

### Users, Departments, Vendors, Expenses, Budgets
- **Schema**: Defined as objects with additional properties allowed. Specific fields are not detailed in the configuration.