# Overview

This document provides a configuration example for integrating with a third-party ERP/CRM REST API using dltHub's REST API source format. The integration focuses on retrieving data through GET operations, supporting various resources such as products, categories, invoices, and users. The configuration is designed to handle pagination, authentication, and error handling in a vendor-neutral manner.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "DOLAPIKEY",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
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
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 1000
                }
            }
        },
        {
            "name": "product_categories",
            "endpoint": {
                "path": "/categories",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "customer_invoices",
            "endpoint": {
                "path": "/invoices",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 1000
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/thirdparties",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 1000
                }
            }
        },
        {
            "name": "suppliers",
            "endpoint": {
                "path": "/thirdparties",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "internal_users",
            "endpoint": {
                "path": "/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.
- **Location**: The API key is included in the request header with the name `DOLAPIKEY`.

# Resources

- **Resource Name**: Abstracted names such as `products`, `product_categories`, `customer_invoices`, `customers`, `suppliers`, `internal_users`.
- **Endpoint Path and Method**: All resources use the `GET` method with paths like `/products`, `/categories`, `/invoices`, `/thirdparties`, `/users`.
- **Pagination Strategy**: Uses `page_number` pagination with parameters `page` and `limit`.
- **Data Selector**: The data is extracted from the `data` field in the response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP errors with appropriate retry mechanisms.

# Schema

- **Fields, Types, Nullability**: The schema includes fields like `product_id`, `category_id`, `invoice_id`, `customer_id`, `supplier_id`, `user_id`, and `company_id`, with types such as `number` and `string`.
- **Neutral Field Names**: Use generic field names like `product_id`, `category_id`, `invoice_id`, `customer_id`, `supplier_id`, `user_id`, and `company_id` to ensure vendor neutrality.