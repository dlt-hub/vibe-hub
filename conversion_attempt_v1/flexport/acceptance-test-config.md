# Overview

This document provides a configuration example for integrating with a third-party logistics API using dltHub's REST API source. The integration supports data synchronization from various resources such as companies, locations, products, shipments, and invoices. The configuration is designed to be vendor-neutral and does not include any proprietary or branded content.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "companies",
            "endpoint": {
                "path": "/v1/companies",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v1/locations",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "shipments",
            "endpoint": {
                "path": "/v1/shipments",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/v1/invoices",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: The API key is stored securely using `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of the request.

## Resources

### Companies
- **Endpoint Path**: `/v1/companies`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

### Locations
- **Endpoint Path**: `/v1/locations`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

### Products
- **Endpoint Path**: `/v1/products`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

### Shipments
- **Endpoint Path**: `/v1/shipments`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

### Invoices
- **Endpoint Path**: `/v1/invoices`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors.
- **HTTP Status Codes**: The system retries on status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `description`, and other relevant attributes for each resource.
- **Types**: Field types are consistent with the data returned by the API.
- **Nullability**: Fields are nullable based on the API's response structure.

This configuration provides a comprehensive setup for integrating with a third-party logistics API using dltHub's REST API source, ensuring a clean and vendor-neutral implementation.