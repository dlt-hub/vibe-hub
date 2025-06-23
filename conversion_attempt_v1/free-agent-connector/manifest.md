# Overview

This document provides a configuration example for integrating with a third-party accounting API using the dltHub REST API source format. The integration allows for the extraction of various resources such as users, categories, transactions, and more, from the API. The configuration supports pagination, error handling, and authentication using OAuth2.

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
            "refresh_token": dlt.secrets["client_refresh_token_2"],
            "token_url": "https://api.example.com/v2/token_endpoint"
        }
    },
    "resource_defaults": {
        "primary_key": "url",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "users",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "admin_expenses_categories",
            "endpoint": {
                "path": "/categories",
                "data_selector": "admin_expenses_categories",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "balance_sheet",
            "endpoint": {
                "path": "/accounting/balance_sheet",
                "data_selector": "balance_sheet"
            }
        },
        {
            "name": "bank_accounts",
            "endpoint": {
                "path": "/bank_accounts",
                "data_selector": "bank_accounts"
            }
        },
        {
            "name": "bank_transaction_explanations",
            "endpoint": {
                "path": "/bank_transaction_explanations",
                "data_selector": "bank_transaction_explanations",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "bills",
            "endpoint": {
                "path": "/bills",
                "data_selector": "bills",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "credit_notes",
            "endpoint": {
                "path": "/credit_notes",
                "data_selector": "credit_notes",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "expenses",
            "endpoint": {
                "path": "/expenses",
                "data_selector": "expenses",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/invoices",
                "data_selector": "invoices",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/projects",
                "data_selector": "projects",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/tasks",
                "data_selector": "tasks",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`
  - `client_secret`
  - `client_refresh_token_2`
- **Token URL**: `https://api.example.com/v2/token_endpoint`

# Resources

- **Resource Name**: Abstracted from the original names
- **Endpoint Path**: Generalized paths such as `/users`, `/categories`, etc.
- **Pagination Strategy**: Page number-based with parameters `page` and `per_page`
- **Data Selector**: Extracts data from specified fields like `users`, `admin_expenses_categories`, etc.

# Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff strategy: Exponential with a factor of 2
- **Timeouts**: Handled via backoff strategies using headers like `Retry-After`

# Schema

- **Fields**: Generalized field names such as `url`, `created_at`, `updated_at`
- **Types**: Includes string, number, boolean, and object types
- **Nullability**: Fields can be nullable, indicated by `"null"` in the type definition

This configuration provides a clean, vendor-neutral setup for integrating with a third-party accounting API using dltHub's REST API source format.