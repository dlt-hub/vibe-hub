# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization for various resources such as products, reports, events, and properties. The API uses basic authentication and supports offset-based pagination.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3/",
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
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
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
            "name": "reports",
            "endpoint": {
                "path": "/v1/products/{product_id}/reports",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
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
            "name": "events",
            "endpoint": {
                "path": "/v1/products/{product_id}/events",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
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
            "name": "properties",
            "endpoint": {
                "path": "/v1/products/{product_id}/properties",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
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
  - `password`: The password for API access.

# Resources

## Products
- **Endpoint Path**: `/v1/products`
- **Pagination**: Offset-based with a limit of 50 records per request.
- **Data Selector**: `data`
- **Primary Key**: `id`

## Reports
- **Endpoint Path**: `/v1/products/{product_id}/reports`
- **Pagination**: Offset-based with a limit of 50 records per request.
- **Data Selector**: `data`
- **Primary Key**: `id`

## Events
- **Endpoint Path**: `/v1/products/{product_id}/events`
- **Pagination**: Offset-based with a limit of 50 records per request.
- **Data Selector**: `data`
- **Primary Key**: `id`

## Properties
- **Endpoint Path**: `/v1/products/{product_id}/properties`
- **Pagination**: Offset-based with a limit of 50 records per request.
- **Data Selector**: `data`
- **Primary Key**: `id`

# Error Handling

- **Retry on Status Codes**: 429 (Rate Limited)
- **Max Retries**: 3
- **Backoff Factor**: 2 (Exponential Backoff)

# Schema

## Products
- **Fields**:
  - `account_id`: number or null
  - `id`: number
  - `km_key`: string or null
  - `name`: string or null
  - `timezone`: string or null

## Reports
- **Fields**:
  - `id`: number
  - `name`: string or null
  - `product_id`: number or null
  - `report_type`: string or null

## Events
- **Fields**:
  - `id`: number
  - `index`: number or null
  - `name`: string or null
  - `product_id`: number or null
  - `property_indices`: array or null
  - `visible`: boolean or null

## Properties
- **Fields**: No specific fields defined, additional properties allowed.