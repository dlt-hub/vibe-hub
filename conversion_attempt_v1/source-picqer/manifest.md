# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources such as customers, products, orders, and more, with capabilities for incremental synchronization and error handling.

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
            "name": "customers",
            "endpoint": {
                "path": "/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
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
                    "limit": 10,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/orders",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: Your API username
  - `password`: Your API password

## Resources

### Customers
- **Endpoint Path**: `/customers`
- **Primary Key**: `idcustomer`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

### Products
- **Endpoint Path**: `/products`
- **Primary Key**: `idproduct`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

### Orders
- **Endpoint Path**: `/orders`
- **Primary Key**: `idorder`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

// Additional resources follow the same structure

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status code 429 (Rate Limited)
  - Maximum retries: 3
  - Exponential backoff with a factor of 2

## Schema

The schema for each resource is defined with neutral field names and types. For example:

### Customers Schema
- **idcustomer**: Number (Primary Key)
- **name**: String
- **emailaddress**: String
- **telephone**: String
- **addresses**: Array of objects containing address details

### Products Schema
- **idproduct**: Number (Primary Key)
- **name**: String
- **price**: Number
- **stock**: Array of objects containing stock details

### Orders Schema
- **idorder**: Number (Primary Key)
- **status**: String
- **created**: String (DateTime)
- **updated**: String (DateTime)

// Additional schemas follow the same pattern

This configuration provides a comprehensive setup for integrating with a third-party analytics API using dltHub's REST API source format, ensuring a clean and vendor-neutral implementation.