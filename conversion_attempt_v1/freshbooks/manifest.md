# Overview

This document provides a configuration guide for integrating with a third-party accounting API using dltHub's REST API source. The integration allows seamless synchronization of invoicing, expenses, and client data into data warehouses or analytics platforms.

## Configuration Example

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
            "refresh_token": dlt.secrets["client_refresh_token"],
            "token_url": "https://api.example.com/auth/oauth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "user_data",
            "endpoint": {
                "path": "/auth/api/v1/users/me",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "client_data",
            "endpoint": {
                "path": "/accounting/account/{account_id}/users/clients",
                "data_selector": "response.result.clients",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "invoice_data",
            "endpoint": {
                "path": "/accounting/account/{account_id}/invoices/invoices",
                "data_selector": "response.result.invoices",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "expense_data",
            "endpoint": {
                "path": "/accounting/account/{account_id}/expenses/expenses",
                "data_selector": "response.result.expenses",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "expense_summary_data",
            "endpoint": {
                "path": "/accounting/account/{account_id}/expenses/summaries",
                "data_selector": "response.result.summaries",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "expense_category_data",
            "endpoint": {
                "path": "/accounting/account/{account_id}/expenses/categories",
                "data_selector": "response.result.categories",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "invoice_detail_data",
            "endpoint": {
                "path": "/accounting/account/{account_id}/reports/accounting/invoice_details",
                "data_selector": "response.result.invoice_details.clients.*.invoices.*",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "expense_detail_data",
            "endpoint": {
                "path": "/accounting/account/{account_id}/reports/accounting/expense_details",
                "data_selector": "response.result.expense_details.data.*.expenses.*",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "account_data",
            "endpoint": {
                "path": "/accounting/businesses/{business_uuid}/ledger_accounts/accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "tax_data",
            "endpoint": {
                "path": "/accounting/account/{account_id}/taxes/taxes",
                "data_selector": "response.result.taxes.*",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets**:
  - `client_id`
  - `client_secret`
  - `client_refresh_token`
- **Token URL**: `https://api.example.com/auth/oauth/token`

## Resources

### User Data
- **Endpoint Path**: `/auth/api/v1/users/me`
- **Pagination**: Page number
- **Data Selector**: `data`

### Client Data
- **Endpoint Path**: `/accounting/account/{account_id}/users/clients`
- **Pagination**: Page number
- **Data Selector**: `response.result.clients`

### Invoice Data
- **Endpoint Path**: `/accounting/account/{account_id}/invoices/invoices`
- **Pagination**: Page number
- **Data Selector**: `response.result.invoices`

### Expense Data
- **Endpoint Path**: `/accounting/account/{account_id}/expenses/expenses`
- **Pagination**: Page number
- **Data Selector**: `response.result.expenses`

### Expense Summary Data
- **Endpoint Path**: `/accounting/account/{account_id}/expenses/summaries`
- **Pagination**: Page number
- **Data Selector**: `response.result.summaries`

### Expense Category Data
- **Endpoint Path**: `/accounting/account/{account_id}/expenses/categories`
- **Pagination**: Page number
- **Data Selector**: `response.result.categories`

### Invoice Detail Data
- **Endpoint Path**: `/accounting/account/{account_id}/reports/accounting/invoice_details`
- **Pagination**: Page number
- **Data Selector**: `response.result.invoice_details.clients.*.invoices.*`

### Expense Detail Data
- **Endpoint Path**: `/accounting/account/{account_id}/reports/accounting/expense_details`
- **Pagination**: Page number
- **Data Selector**: `response.result.expense_details.data.*.expenses.*`

### Account Data
- **Endpoint Path**: `/accounting/businesses/{business_uuid}/ledger_accounts/accounts`
- **Pagination**: Page number
- **Data Selector**: `data`

### Tax Data
- **Endpoint Path**: `/accounting/account/{account_id}/taxes/taxes`
- **Pagination**: Page number
- **Data Selector**: `response.result.taxes.*`

## Error Handling

- **Retry Logic**: Implement retry on status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

- **User Data**: Includes fields like `id`, `email`, `first_name`, `last_name`
- **Client Data**: Includes fields like `id`, `email`, `organization`
- **Invoice Data**: Includes fields like `id`, `invoice_number`, `total`
- **Expense Data**: Includes fields like `id`, `amount`, `date`
- **Expense Summary Data**: Includes fields like `id`, `amounts`
- **Expense Category Data**: Includes fields like `id`, `category`
- **Invoice Detail Data**: Includes fields like `invoiceid`, `total`
- **Expense Detail Data**: Includes fields like `expenseid`, `amount`
- **Account Data**: Includes fields like `id`, `name`, `type`
- **Tax Data**: Includes fields like `taxid`, `name`, `amount`