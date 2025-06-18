# Overview

This document provides a configuration example for integrating with a third-party budgeting API using dltHub's REST API source format. The integration allows for the replication of various financial resources such as budgets, accounts, categories, payees, transactions, and category groups. The API requires authentication via a personal access token.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
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
            "name": "budgets",
            "endpoint": {
                "path": "/budgets",
                "data_selector": ["data", "budgets"]
            }
        },
        {
            "name": "accounts",
            "endpoint": {
                "path": "/budgets/{budget_id}/accounts",
                "data_selector": ["data", "accounts"]
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/budgets/{budget_id}/categories",
                "data_selector": ["data", "category_groups", "*", "categories", "*"]
            }
        },
        {
            "name": "payees",
            "endpoint": {
                "path": "/budgets/{budget_id}/payees",
                "data_selector": ["data", "payees"]
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/budgets/{budget_id}/transactions",
                "data_selector": ["data", "transactions"]
            }
        },
        {
            "name": "category_groups",
            "endpoint": {
                "path": "/budgets/{budget_id}/categories/{category_id}",
                "data_selector": ["data", "category_groups"]
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

- **Budgets**
  - **Endpoint Path**: `/budgets`
  - **Data Selector**: `["data", "budgets"]`
  - **Primary Key**: `id`

- **Accounts**
  - **Endpoint Path**: `/budgets/{budget_id}/accounts`
  - **Data Selector**: `["data", "accounts"]`
  - **Primary Key**: `id`

- **Categories**
  - **Endpoint Path**: `/budgets/{budget_id}/categories`
  - **Data Selector**: `["data", "category_groups", "*", "categories", "*"]`

- **Payees**
  - **Endpoint Path**: `/budgets/{budget_id}/payees`
  - **Data Selector**: `["data", "payees"]`

- **Transactions**
  - **Endpoint Path**: `/budgets/{budget_id}/transactions`
  - **Data Selector**: `["data", "transactions"]`
  - **Primary Key**: `id`

- **Category Groups**
  - **Endpoint Path**: `/budgets/{budget_id}/categories/{category_id}`
  - **Data Selector**: `["data", "category_groups"]`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a schema that defines the fields, types, and nullability. The schemas are designed to be flexible and accommodate various data types, ensuring compatibility with the API's responses. The field names are generalized to maintain neutrality and avoid any proprietary references.