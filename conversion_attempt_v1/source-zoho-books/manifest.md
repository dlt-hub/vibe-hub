# Overview

This document provides a configuration guide for integrating with a generic financial data API. The integration automates the flow of various financial data such as invoices, payments, expenses, and bank transactions into your data systems. This setup helps streamline bookkeeping processes, ensuring accurate financial reporting and real-time insights.

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
            "refresh_token": dlt.secrets["refresh_token"],
            "token_url": "https://auth.example.com/oauth/v2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "users",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "banking",
            "endpoint": {
                "path": "/v1/bankaccounts",
                "data_selector": "bankaccounts",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v1/organizations",
                "data_selector": "organizations",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "bills",
            "endpoint": {
                "path": "/v1/bills",
                "data_selector": "bills",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "estimates",
            "endpoint": {
                "path": "/v1/estimates",
                "data_selector": "estimates",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "items",
            "endpoint": {
                "path": "/v1/items",
                "data_selector": "items",
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
                "path": "/v1/invoices",
                "data_selector": "invoices",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "expenses",
            "endpoint": {
                "path": "/v1/expenses",
                "data_selector": "expenses",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "creditnotes",
            "endpoint": {
                "path": "/v1/creditnotes",
                "data_selector": "creditnotes",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "customer_payments",
            "endpoint": {
                "path": "/v1/customerpayments",
                "data_selector": "customerpayments",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "purchase_orders",
            "endpoint": {
                "path": "/v1/purchaseorders",
                "data_selector": "purchaseorders",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "sales_orders",
            "endpoint": {
                "path": "/v1/salesorders",
                "data_selector": "salesorders",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "journals",
            "endpoint": {
                "path": "/v1/journals",
                "data_selector": "journals",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "taxes",
            "endpoint": {
                "path": "/v1/settings/taxes",
                "data_selector": "taxes",
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
                "path": "/v1/banktransactions",
                "data_selector": "banktransactions",
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

- **Type**: OAuth2
- **Secrets or Config Values**:
  - `client_id`: Your OAuth client ID
  - `client_secret`: Your OAuth client secret
  - `refresh_token`: Your OAuth refresh token
  - `token_url`: The URL to refresh the OAuth token

# Resources

- **Resource Name**: Abstracted from the stream name
- **Endpoint Path and Method**: Each resource has a specific path and uses the GET method
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `per_page`
- **Data Selector**: Extracts data from the specified field path

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors
- **Known HTTP Response Codes**: Handle common HTTP status codes like 429, 500, 502, 503, 504
- **Fallback/Handling Behavior**: Implement fallback mechanisms for error scenarios

# Schema

- **Fields, Types, Nullability**: Each resource has a defined schema with fields, types, and nullability
- **Neutral Field Names**: Use generic field names like `customer_id`, `transaction_id`, etc.