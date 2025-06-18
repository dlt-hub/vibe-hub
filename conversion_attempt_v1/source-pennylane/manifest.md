# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and pagination strategy. The API uses bearer token authentication and supports incremental synchronization based on a datetime cursor.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
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
            "name": "supplier_invoices",
            "endpoint": {
                "path": "/v1/supplier_invoices",
                "data_selector": "invoices",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 1000
                }
            }
        },
        {
            "name": "suppliers",
            "endpoint": {
                "path": "/v1/suppliers",
                "data_selector": "suppliers",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "plan_items",
            "endpoint": {
                "path": "/v1/plan_items",
                "data_selector": "plan_items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 1000
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "customers",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "customer_invoices",
            "endpoint": {
                "path": "/v1/customer_invoices",
                "data_selector": "invoices",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 1000
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "products",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "category_groups",
            "endpoint": {
                "path": "/v1/category_groups",
                "data_selector": "category_groups",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 1000
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/v1/categories",
                "data_selector": "categories",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "customer_estimates",
            "endpoint": {
                "path": "/v1/customer_estimates",
                "data_selector": "estimates",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 1000
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

Each resource is configured with a specific endpoint path and pagination strategy. The data selector specifies the field path to extract data from the API response.

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Generalized to `/v1/resource_name`
- **Pagination Strategy**: Page number-based with parameters `page` and `per_page`

# Error Handling

The configuration does not explicitly define error handling, but it is recommended to implement retry logic for common HTTP status codes such as 429, 500, 502, 503, and 504. A typical error handler might include:

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

# Schema

The schema for each resource is defined with neutral field names and types. The primary key and updated_at fields are required for incremental synchronization.

- **Fields**: Defined in a neutral manner, e.g., `id`, `updated_at`
- **Types**: Include string, number, boolean, and nullability where applicable
- **Primary Key**: Used for deduplication and incremental sync

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format.