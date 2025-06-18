# Overview

This document provides a configuration example for integrating with a third-party billing API using the dltHub REST API source. The integration allows for data extraction from various resources such as products, plans, addons, coupons, customers, quotes, invoices, expenses, subscriptions, taxes, transactions, and recurring expenses. The API supports OAuth2 authentication and uses a page number-based pagination strategy.

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
            "token_url": "https://accounts.example.com/oauth/v2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "products",
            "endpoint": {
                "path": "/products",
                "data_selector": "products",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "plans",
            "endpoint": {
                "path": "/plans",
                "data_selector": "plans",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "addons",
            "endpoint": {
                "path": "/addons",
                "data_selector": "addons",
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
                "path": "/coupons",
                "data_selector": "coupons",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/customers",
                "data_selector": "customers",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "quotes",
            "endpoint": {
                "path": "/estimates",
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
            "name": "invoices",
            "endpoint": {
                "path": "/invoices",
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
                "path": "/expenses",
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
            "name": "subscriptions",
            "endpoint": {
                "path": "/subscriptions",
                "data_selector": "subscriptions",
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
                "path": "/settings/taxes",
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
                "path": "/transactions",
                "data_selector": "transactions",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "recurring_expenses",
            "endpoint": {
                "path": "/recurringexpenses",
                "data_selector": "recurring_expenses",
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
- **Secrets**: 
  - `client_id`
  - `client_secret`
  - `refresh_token`
- **Token URL**: `https://accounts.example.com/oauth/v2/token`

# Resources

Each resource is configured with a specific endpoint path and pagination strategy. The data selector is used to extract the relevant data from the API response.

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Generalized paths for each resource
- **Pagination Strategy**: Page number-based with parameters `page` and `per_page`
- **Data Selector**: Extracts data from the specified field in the response

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors
- **HTTP Status Codes**: Handle common HTTP errors such as 429, 500, 502, 503, and 504
- **Backoff Strategy**: Use exponential backoff for retries

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for each resource is specified to ensure data integrity.

- **Fields**: Defined with types and nullability
- **Primary Key**: Ensures uniqueness and consistency of data

This configuration provides a clean and generalized setup for integrating with a third-party billing API using dltHub's REST API source.