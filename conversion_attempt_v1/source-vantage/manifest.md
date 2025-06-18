# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports multiple resources, each with its own endpoint and schema. The API uses bearer token authentication and supports pagination through page increments.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["access_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "providers",
            "endpoint": {
                "path": "/v1/providers",
                "data_selector": "providers",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "services",
            "endpoint": {
                "path": "/v1/services",
                "data_selector": "services",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
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
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "reports",
            "endpoint": {
                "path": "/v1/reports",
                "data_selector": "reports",
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

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["access_token"]`

## Resources

### Providers

- **Endpoint Path**: `/v1/providers`
- **Data Selector**: `providers`
- **Pagination**: Page number with parameters `page` and `limit` set to 100

### Services

- **Endpoint Path**: `/v1/services`
- **Data Selector**: `services`
- **Pagination**: Page number with parameters `page` and `limit` set to 100

### Products

- **Endpoint Path**: `/v1/products`
- **Data Selector**: `products`
- **Pagination**: Page number with parameters `page` and `limit` set to 100

### Reports

- **Endpoint Path**: `/v1/reports`
- **Data Selector**: `reports`
- **Pagination**: Page number with parameters `page` and `limit` set to 100

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504) with a maximum of 3 retries and a backoff factor of 2.

## Schema

### Providers

- **Fields**:
  - `id`: string
  - `name`: string, description: The common name of the provider.
  - `description`: string, description: The full descriptive name of the provider.

### Services

- **Fields**:
  - `id`: string
  - `name`: string, description: The common name of the service.
  - `description`: string, description: The full name of the service.
  - `category`: string, description: The type of cloud service.

### Products

- **Fields**:
  - `id`: string
  - `name`: string, description: The common name of the product.
  - `category`: string, description: The category of the cloud product.
  - `details`: object, description: An object of metadata about the product.
  - `provider_id`: string, description: A unique slug for the provider the product belongs to.
  - `service_id`: string, description: A unique slug for the service the product belongs to.

### Reports

- **Fields**:
  - `id`: string
  - `title`: string, description: The title of the cost report.
  - `created_at`: string, description: The date and time, in UTC, the report was created. ISO 8601 Formatted.
  - `earliest_cost_date`: string, description: The date and time, in UTC, the report was created. ISO 8601 Formatted.
  - `latest_cost_date`: string, description: The date and time, in UTC, the report was created. ISO 8601 Formatted.
  - `workspace`: string, description: The name of the workspace the report is a part of.