# Overview

This document provides a configuration guide for integrating with a third-party payment processing API using dltHub's REST API source format. The integration allows for the extraction of data related to charges, products, customers, and subscriptions. The API supports pagination and requires authentication via basic HTTP credentials.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "charges",
            "endpoint": {
                "path": "/charges",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/products",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "subscriptions",
            "endpoint": {
                "path": "/subscriptions",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic HTTP Authentication
- **Secrets**: 
  - `username`: Your API username
  - `password`: Your API password

## Resources

### Charges
- **Endpoint Path**: `/charges`
- **Primary Key**: `charge_id`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

### Products
- **Endpoint Path**: `/products`
- **Primary Key**: `product_id`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

### Customers
- **Endpoint Path**: `/customers`
- **Primary Key**: `customer_id`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

### Subscriptions
- **Endpoint Path**: `/subscriptions`
- **Primary Key**: `subscription_id`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry on status codes [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Charges
- **Fields**: 
  - `charge_id` (string)
  - `occurred` (string)
  - Additional fields include `description`, `amount`, `status`, etc.

### Products
- **Fields**: 
  - `product_id` (string)
  - Additional fields include `description`, `amount`, `status`, etc.

### Customers
- **Fields**: 
  - `customer_id` (string)
  - `first_seen` (number)
  - Additional fields include `name`, `email`, `phone`, etc.

### Subscriptions
- **Fields**: 
  - `subscription_id` (string)
  - `first_seen` (number)
  - Additional fields include `status`, `amount`, `currency`, etc.

This configuration provides a comprehensive setup for accessing and synchronizing data from a third-party payment processing API using dltHub's REST API source.