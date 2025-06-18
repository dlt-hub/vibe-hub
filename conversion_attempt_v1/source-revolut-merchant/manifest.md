# Overview

This document provides a configuration guide for integrating with a third-party merchant API using dltHub's REST API source. The integration allows for the ingestion of data from various endpoints, including orders, customers, and locations. The API supports authentication via bearer tokens and offers pagination and incremental sync capabilities.

## Configuration Example

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
            "name": "orders",
            "endpoint": {
                "path": "/v1/orders",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "data"
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v1/locations",
                "data_selector": "data"
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from dlt secrets using the key `api_key`.

## Resources

### Orders

- **Endpoint Path**: `/v1/orders`
- **Pagination Strategy**: Offset
  - **Limit**: 1000
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Incremental Sync**: Based on `created_at` field

### Customers

- **Endpoint Path**: `/v1/customers`
- **Data Selector**: `data`
- **Primary Key**: `id`

### Locations

- **Endpoint Path**: `/v1/locations`
- **Data Selector**: `data`
- **Primary Key**: `id`

## Error Handling

- **Retry Logic**: Implemented for HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Orders

- **Fields**:
  - `id`: string
  - `created_at`: string
  - `type`: string or null
  - `metadata`: object or null
  - `capture_mode`: string or null
  - `order_amount`: object or null
    - `currency`: string or null
    - `value`: number or null
  - `order_outstanding_amount`: object or null
    - `currency`: string or null
    - `value`: number or null
  - `state`: string or null
  - `updated_at`: string or null

### Customers

- **Fields**:
  - `id`: string
  - `business_name`: string or null
  - `created_at`: string or null
  - `email`: string or null
  - `full_name`: string or null
  - `phone`: string or null
  - `updated_at`: string or null

### Locations

- **Fields**:
  - `id`: string
  - `type`: string or null
  - `details`: object or null
    - `domain`: string or null
  - `name`: string or null

This configuration provides a clean and vendor-neutral setup for integrating with a third-party merchant API using dltHub's REST API source.