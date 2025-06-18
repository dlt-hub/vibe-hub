# Overview

This document provides a configuration example for integrating with a third-party inventory management API using the dltHub REST API source. The integration allows for seamless data synchronization of inventory-related resources such as items, orders, vendors, and invoices, ensuring up-to-date insights for analytics and reporting.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/inventory/v1",
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
            "name": "items",
            "endpoint": {
                "path": "/items",
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
            "name": "item_groups",
            "endpoint": {
                "path": "/itemgroups",
                "data_selector": "itemgroups",
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
            "name": "contacts",
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
            "name": "item_adjustments",
            "endpoint": {
                "path": "/inventoryadjustments",
                "data_selector": "inventory_adjustments",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "warehouses",
            "endpoint": {
                "path": "/settings/warehouses",
                "data_selector": "warehouses"
            }
        },
        {
            "name": "transfer_orders",
            "endpoint": {
                "path": "/transferorders",
                "data_selector": "transfer_orders",
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
                "path": "/salesorders",
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
            "name": "packages",
            "endpoint": {
                "path": "/packages",
                "data_selector": "packages",
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
            "name": "purchase_orders",
            "endpoint": {
                "path": "/purchaseorders",
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
            "name": "credit_notes",
            "endpoint": {
                "path": "/creditnotes",
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

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Generalized paths such as `/items`, `/itemgroups`, etc.
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `per_page`
- **Data Selector**: Extracts data from fields like `items`, `itemgroups`, etc.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

- **Fields**: Use neutral field names such as `id`, `name`, `status`, etc.
- **Types**: Include types like `string`, `number`, `boolean`, and handle nullability.
- **Primary Key**: Defined for each resource, e.g., `item_id`, `group_id`, etc.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party inventory management API using dltHub's REST API source.