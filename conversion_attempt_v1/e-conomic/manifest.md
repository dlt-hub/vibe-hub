# Overview

This document provides a configuration example for integrating with a third-party accounting API using the dltHub REST API source. The integration allows for the extraction of various financial data such as accounts, customers, invoices, and more, facilitating automated data extraction for comprehensive financial reporting and analysis.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "X-AppSecretToken",
            "api_key": dlt.secrets["app_secret_token"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "accounts",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "accounting_years",
            "endpoint": {
                "path": "/v1/accounting-years",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "app_roles",
            "endpoint": {
                "path": "/v1/app-roles",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "currencies",
            "endpoint": {
                "path": "/v1/currencies",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "customer_groups",
            "endpoint": {
                "path": "/v1/customer-groups",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "departmental_distributions",
            "endpoint": {
                "path": "/v1/departmental-distributions",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "departments",
            "endpoint": {
                "path": "/v1/departments",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "employees",
            "endpoint": {
                "path": "/v1/employees",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "journals",
            "endpoint": {
                "path": "/v1/journals",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "payment_terms",
            "endpoint": {
                "path": "/v1/payment-terms",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "payment_types",
            "endpoint": {
                "path": "/v1/payment-types",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "product_groups",
            "endpoint": {
                "path": "/v1/product-groups",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "draft_quotes",
            "endpoint": {
                "path": "/v1/quotes/drafts",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "suppliers",
            "endpoint": {
                "path": "/v1/suppliers",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "units",
            "endpoint": {
                "path": "/v1/units",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "vat_accounts",
            "endpoint": {
                "path": "/v1/vat-accounts",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "vat_types",
            "endpoint": {
                "path": "/v1/vat-types",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "vat_zones",
            "endpoint": {
                "path": "/v1/vat-zones",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "sent_quotes",
            "endpoint": {
                "path": "/v1/quotes/sent",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "archived_quotes",
            "endpoint": {
                "path": "/v1/quotes/archived",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "draft_invoices",
            "endpoint": {
                "path": "/v1/invoices/drafts",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "booked_invoices",
            "endpoint": {
                "path": "/v1/invoices/booked",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "paid_invoices",
            "endpoint": {
                "path": "/v1/invoices/paid",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "overdue_invoices",
            "endpoint": {
                "path": "/v1/invoices/overdue",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "unpaid_invoices",
            "endpoint": {
                "path": "/v1/invoices/unpaid",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "not_due_invoices",
            "endpoint": {
                "path": "/v1/invoices/not-due",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "total_invoices",
            "endpoint": {
                "path": "/v1/invoices/totals",
                "data_selector": [],
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "sent_invoices",
            "endpoint": {
                "path": "/v1/invoices/sent",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "draft_orders",
            "endpoint": {
                "path": "/v1/orders/drafts",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "sent_orders",
            "endpoint": {
                "path": "/v1/orders/sent",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "archived_orders",
            "endpoint": {
                "path": "/v1/orders/archived",
                "data_selector": "collection",
                "paginator": {
                    "type": "page_number",
                    "page_param": "skippages",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: 
  - `app_secret_token`: Your private token that identifies your app.
  - `agreement_grant_token`: Token that identifies the grant issued by an agreement, allowing your app to access data.

# Resources

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized paths for each resource.
- **Pagination Strategy**: Uses `page_number` pagination with parameters `skippages` and `page_size`.
- **Data Selector**: Extracts data from the `collection` field.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Factor**: Use a backoff factor of 2 for retries.
- **Max Retries**: Set a maximum of 3 retries for failed requests.

# Schema

- **Fields**: Use neutral field names such as `id`, `customer_id`, `product_id`, etc.
- **Types**: Consistent with the original schema but generalized for public use.
- **Nullability**: Fields can be nullable as per the original schema definitions.