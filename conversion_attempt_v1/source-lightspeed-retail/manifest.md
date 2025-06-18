# Overview

This document provides a configuration guide for integrating with a generic third-party retail API using the dltHub REST API source. The integration allows for the extraction of various resources such as users, customers, and sales data. The API requires authentication via an API key, and supports pagination and error handling mechanisms.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
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
            "name": "users",
            "endpoint": {
                "path": "/api/2.0/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 200
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/api/2.0/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "audit",
            "endpoint": {
                "path": "/api/2.0/security_events",
                "data_selector": "data"
            }
        },
        {
            "name": "brands",
            "endpoint": {
                "path": "/api/2.0/brands",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "attributes",
            "endpoint": {
                "path": "/api/2.0/variant_attributes",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "taxes",
            "endpoint": {
                "path": "/api/2.0/taxes",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/api/2.0/tags",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "suppliers",
            "endpoint": {
                "path": "/api/2.0/suppliers",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "serial_numbers",
            "endpoint": {
                "path": "/api/2.0/serialnumbers",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "sales",
            "endpoint": {
                "path": "/api/2.0/sales",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "registers",
            "endpoint": {
                "path": "/api/2.0/registers",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "quotes",
            "endpoint": {
                "path": "/api/2.0/quotes",
                "data_selector": "Quotes",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "services",
            "endpoint": {
                "path": "/api/2.0/services",
                "data_selector": "jobs",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "promotions",
            "endpoint": {
                "path": "/api/2.0/promotions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/api/2.0/products",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "product_categories",
            "endpoint": {
                "path": "/api/2.0/product_categories",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "price_books",
            "endpoint": {
                "path": "/api/2.0/price_book_products",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "payment_types",
            "endpoint": {
                "path": "/api/2.0/payment_types",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "outlets",
            "endpoint": {
                "path": "/api/2.0/outlets",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "inventory",
            "endpoint": {
                "path": "/api/2.0/inventory",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "fulfillments",
            "endpoint": {
                "path": "/api/2.0/sales/{{ stream_partition.sale_id }}/fulfillments",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "customer_groups",
            "endpoint": {
                "path": "/api/2.0/customer_groups",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "consignments",
            "endpoint": {
                "path": "/api/2.0/consignments",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "consignment_products",
            "endpoint": {
                "path": "/api/2.0/consignments/{{stream_partition.consignment_id}}/products",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **API Key**: Stored securely in `dlt.secrets["api_key"]`
- **Location**: Header

## Resources

### Users
- **Endpoint Path**: `/api/2.0/users`
- **Data Selector**: `data`
- **Pagination**: Page number with `page` and `page_size` parameters, limit 200

### Customers
- **Endpoint Path**: `/api/2.0/customers`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Audit
- **Endpoint Path**: `/api/2.0/security_events`
- **Data Selector**: `data`

### Brands
- **Endpoint Path**: `/api/2.0/brands`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Attributes
- **Endpoint Path**: `/api/2.0/variant_attributes`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Taxes
- **Endpoint Path**: `/api/2.0/taxes`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Tags
- **Endpoint Path**: `/api/2.0/tags`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Suppliers
- **Endpoint Path**: `/api/2.0/suppliers`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Serial Numbers
- **Endpoint Path**: `/api/2.0/serialnumbers`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Sales
- **Endpoint Path**: `/api/2.0/sales`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Registers
- **Endpoint Path**: `/api/2.0/registers`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Quotes
- **Endpoint Path**: `/api/2.0/quotes`
- **Data Selector**: `Quotes`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Services
- **Endpoint Path**: `/api/2.0/services`
- **Data Selector**: `jobs`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Promotions
- **Endpoint Path**: `/api/2.0/promotions`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Products
- **Endpoint Path**: `/api/2.0/products`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Product Categories
- **Endpoint Path**: `/api/2.0/product_categories`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Price Books
- **Endpoint Path**: `/api/2.0/price_book_products`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Payment Types
- **Endpoint Path**: `/api/2.0/payment_types`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Outlets
- **Endpoint Path**: `/api/2.0/outlets`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Inventory
- **Endpoint Path**: `/api/2.0/inventory`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Fulfillments
- **Endpoint Path**: `/api/2.0/sales/{{ stream_partition.sale_id }}/fulfillments`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Customer Groups
- **Endpoint Path**: `/api/2.0/customer_groups`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Consignments
- **Endpoint Path**: `/api/2.0/consignments`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

### Consignment Products
- **Endpoint Path**: `/api/2.0/consignments/{{stream_partition.consignment_id}}/products`
- **Data Selector**: `data`
- **Pagination**: Cursor with `after` and `page_size` parameters, limit 100

## Error Handling

- **Retry Logic**: Implemented for HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with various data models. Each resource has a primary key field, typically named `id`, and includes additional properties as specified in the API documentation.