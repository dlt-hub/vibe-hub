# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization for various resources such as invoices, recurring invoices, products, revenues, and customers. The API uses a POST method for data retrieval and supports offset-based pagination.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "invoices",
            "endpoint": {
                "path": "/api.php",
                "method": "POST",
                "data_selector": ["RESPONSE", "INVOICES"],
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "OFFSET"
                }
            }
        },
        {
            "name": "recurring_invoices",
            "endpoint": {
                "path": "/api.php",
                "method": "POST",
                "data_selector": ["RESPONSE", "INVOICES"],
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "OFFSET"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/api.php",
                "method": "POST",
                "data_selector": ["RESPONSE", "ARTICLES"],
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "OFFSET"
                }
            }
        },
        {
            "name": "revenues",
            "endpoint": {
                "path": "/api.php",
                "method": "POST",
                "data_selector": ["RESPONSE", "REVENUES"],
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "OFFSET"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/api.php",
                "method": "POST",
                "data_selector": ["RESPONSE", "CUSTOMERS"],
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "OFFSET"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: The username for API access.
  - `api_key`: The API key for authentication.

# Resources

### Invoices
- **Endpoint Path**: `/api.php`
- **HTTP Method**: POST
- **Data Selector**: `["RESPONSE", "INVOICES"]`
- **Primary Key**: `INVOICE_ID`
- **Pagination**: Offset-based with `OFFSET` as the offset parameter and a limit of 100 records per request.

### Recurring Invoices
- **Endpoint Path**: `/api.php`
- **HTTP Method**: POST
- **Data Selector**: `["RESPONSE", "INVOICES"]`
- **Primary Key**: `INVOICE_ID`
- **Pagination**: Offset-based with `OFFSET` as the offset parameter and a limit of 100 records per request.

### Products
- **Endpoint Path**: `/api.php`
- **HTTP Method**: POST
- **Data Selector**: `["RESPONSE", "ARTICLES"]`
- **Primary Key**: `ARTICLE_ID`
- **Pagination**: Offset-based with `OFFSET` as the offset parameter and a limit of 100 records per request.

### Revenues
- **Endpoint Path**: `/api.php`
- **HTTP Method**: POST
- **Data Selector**: `["RESPONSE", "REVENUES"]`
- **Primary Key**: `INVOICE_ID`
- **Pagination**: Offset-based with `OFFSET` as the offset parameter and a limit of 100 records per request.

### Customers
- **Endpoint Path**: `/api.php`
- **HTTP Method**: POST
- **Data Selector**: `["RESPONSE", "CUSTOMERS"]`
- **Primary Key**: `CUSTOMER_ID`
- **Pagination**: Offset-based with `OFFSET` as the offset parameter and a limit of 100 records per request.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts
- **Backoff Factor**: 2 (exponential backoff)

# Schema

The schema for each resource is defined with fields, types, and descriptions. Field names are generalized to ensure neutrality. For example, `INVOICE_ID` is used as a primary key for invoices and revenues, while `CUSTOMER_ID` is used for customers. Each field is described with its type and purpose, ensuring clarity and consistency across resources.