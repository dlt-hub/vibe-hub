# Overview

This document provides a configuration example for integrating with a generic third-party expense management API using the dltHub REST API source. The integration allows for seamless data synchronization, automating workflows by extracting financial data efficiently to ensure accurate reporting and streamlined operations.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/expense/v1",
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
            "name": "users",
            "endpoint": {
                "path": "/users",
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
            "name": "trips",
            "endpoint": {
                "path": "/trips",
                "data_selector": "trips",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "expense_reports",
            "endpoint": {
                "path": "/expensereports",
                "data_selector": "expense_reports",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
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
                    "limit": 200
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/contacts",
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
            "name": "organizations",
            "endpoint": {
                "path": "/organizations",
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
            "name": "expense_categories",
            "endpoint": {
                "path": "/expensecategories",
                "data_selector": "expense_accounts",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "currencies",
            "endpoint": {
                "path": "/settings/currencies",
                "data_selector": "currencies",
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

Each resource is configured with a specific endpoint path and pagination strategy. The data selector specifies the field path to extract data from the API response.

- **Users**
  - **Endpoint Path**: `/users`
  - **Data Selector**: `users`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **Trips**
  - **Endpoint Path**: `/trips`
  - **Data Selector**: `trips`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **Expense Reports**
  - **Endpoint Path**: `/expensereports`
  - **Data Selector**: `expense_reports`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **Projects**
  - **Endpoint Path**: `/projects`
  - **Data Selector**: `projects`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **Customers**
  - **Endpoint Path**: `/contacts`
  - **Data Selector**: `contacts`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **Organizations**
  - **Endpoint Path**: `/organizations`
  - **Data Selector**: `organizations`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **Expense Categories**
  - **Endpoint Path**: `/expensecategories`
  - **Data Selector**: `expense_accounts`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **Currencies**
  - **Endpoint Path**: `/settings/currencies`
  - **Data Selector**: `currencies`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **Taxes**
  - **Endpoint Path**: `/settings/taxes`
  - **Data Selector**: `taxes`
  - **Pagination**: Page number with `page` and `per_page` parameters

# Error Handling

The configuration does not explicitly define error handling strategies. However, typical error handling in REST API integrations includes retry logic for transient errors, handling specific HTTP status codes, and implementing backoff strategies.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for each resource is specified to ensure data integrity and uniqueness.