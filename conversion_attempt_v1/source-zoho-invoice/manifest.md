# Overview

This document provides a configuration example for integrating with a third-party invoicing API using the dltHub REST API source. The integration allows for data extraction from various resources such as items, users, contacts, invoices, and more. The API supports OAuth2 authentication and uses offset-based pagination for data retrieval.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/invoice/v3/",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://accounts.example.com/oauth/v2/token",
            "refresh_token": dlt.secrets["client_refresh_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "items",
            "endpoint": {
                "path": "/items",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "users",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/invoices",
                "data_selector": "invoices",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "recurring_invoices",
            "endpoint": {
                "path": "/recurringinvoices",
                "data_selector": "recurring_invoices",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "customer_payments",
            "endpoint": {
                "path": "/customerpayments",
                "data_selector": "customerpayments",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "credit_notes",
            "endpoint": {
                "path": "/creditnotes",
                "data_selector": "creditnotes",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "expenses",
            "endpoint": {
                "path": "/expenses",
                "data_selector": "expenses",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "taxes",
            "endpoint": {
                "path": "/settings/taxes",
                "data_selector": "taxes",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "page_param": "page",
                    "limit_param": "per_page"
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
- **Token URL**: `https://accounts.example.com/oauth/v2/token`
- **Refresh Token**: `dlt.secrets["client_refresh_token"]`

# Resources

- **Resource Name**: items, users, contacts, invoices, recurring_invoices, customer_payments, credit_notes, expenses, taxes
- **Endpoint Path**: `/items`, `/users`, `/contacts`, `/invoices`, `/recurringinvoices`, `/customerpayments`, `/creditnotes`, `/expenses`, `/settings/taxes`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Page Parameter**: `page`
  - **Limit Parameter**: `per_page`
  - **Limit**: 200
- **Data Selector**: Corresponding to each resource name

# Error Handling

- **Retry Logic**: Implement retry logic with exponential backoff for HTTP status codes 429, 500, 502, 503, 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Fields**: Each resource has its own schema with fields such as `id`, `name`, `status`, etc.
- **Types**: String, number, boolean, object, array
- **Nullability**: Fields can be nullable
- **Primary Key**: Unique identifier for each resource (e.g., `item_id`, `user_id`, `contact_id`, etc.)