# Overview

This document provides a configuration example for integrating with a third-party asset and employee data API using dltHub's REST API source. The integration supports fetching data from two resources: assets and employees, with incremental synchronization based on update timestamps.

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
            "name": "assets",
            "endpoint": {
                "path": "/api/v1/assets",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "employees",
            "endpoint": {
                "path": "/api/v1/employees",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer Token
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Assets

- **Endpoint Path**: `/api/v1/assets`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 50
  - **Offset Parameter**: `offset`
- **Data Selector**: `data`
- **Primary Key**: `id`

### Employees

- **Endpoint Path**: `/api/v1/employees`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 50
  - **Offset Parameter**: `offset`
- **Data Selector**: `data`
- **Primary Key**: `id`

## Error Handling

- **Retry Logic**: Implemented with exponential backoff
- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Assets

- **Fields**:
  - `asset_id`: string or null
  - `asset_name`: string or null
  - `asset_serial`: string or null
  - `category_id`: number or null
  - `category_name`: string or null
  - `checkout_date`: string or null
  - `created_at`: string or null
  - `currency`: string or null
  - `department_id`: number or null
  - `deployed_to`: array or null
  - `end_of_life_date`: string or null
  - `id`: number
  - `location_id`: number or null
  - `manufacturer_id`: number or null
  - `manufacturer_name`: string or null
  - `notes`: string or null
  - `order_number`: string or null
  - `product_id`: number or null
  - `product_name`: string or null
  - `purchase_cost`: number or null
  - `purchase_date`: string or null
  - `status_id`: number or null
  - `updated_at`: string
  - `warranty_expiration_date`: string or null

### Employees

- **Fields**:
  - `archived`: boolean or null
  - `assets`: array or null
  - `created_at`: string or null
  - `department_id`: number or null
  - `email`: string or null
  - `id`: number
  - `name`: string or null
  - `notes`: string or null
  - `number_of_accessories`: number or null
  - `number_of_assets`: number or null
  - `number_of_consumables`: number or null
  - `number_of_licenses`: number or null
  - `title`: string or null
  - `updated_at`: string

This configuration provides a clean and generalized setup for integrating with a third-party API using dltHub's REST API source, ensuring a vendor-neutral approach.