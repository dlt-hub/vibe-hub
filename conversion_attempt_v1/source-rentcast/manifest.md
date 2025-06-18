# Overview

This document provides a configuration example for integrating with a third-party property analytics API using the dltHub REST API source. The integration allows for data extraction from various endpoints, including property records, sale listings, rental listings, and more. The API supports offset-based pagination and requires an API key for authentication.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "api_key",
            "name": "X-Api-Key",
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
            "name": "property_records",
            "endpoint": {
                "path": "/properties",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "sale_listings",
            "endpoint": {
                "path": "/listings/sale",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "rental_listings",
            "endpoint": {
                "path": "/listings/rental/long-term",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "statistics",
            "endpoint": {
                "path": "/markets",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "value_estimate",
            "endpoint": {
                "path": "/avm/value",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "rent_estimate",
            "endpoint": {
                "path": "/avm/rent/long-term",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Header Name**: `X-Api-Key`
- **API Key**: Stored securely in `dlt.secrets["api_key"]`

## Resources

### Property Records
- **Endpoint Path**: `/properties`
- **Pagination**: Offset-based with `limit` of 500 and `offset` parameter
- **Data Selector**: `data`

### Sale Listings
- **Endpoint Path**: `/listings/sale`
- **Pagination**: Offset-based with `limit` of 500 and `offset` parameter
- **Data Selector**: `data`

### Rental Listings
- **Endpoint Path**: `/listings/rental/long-term`
- **Pagination**: Offset-based with `limit` of 500 and `offset` parameter
- **Data Selector**: `data`
- **Error Handling**: Retries on status code 400 with a maximum of 3 retries and a backoff factor of 2

### Statistics
- **Endpoint Path**: `/markets`
- **Data Selector**: `data`
- **Error Handling**: Retries on status code 400 with a maximum of 3 retries and a backoff factor of 2

### Value Estimate
- **Endpoint Path**: `/avm/value`
- **Data Selector**: `data`
- **Error Handling**: Retries on status code 400 with a maximum of 3 retries and a backoff factor of 2

### Rent Estimate
- **Endpoint Path**: `/avm/rent/long-term`
- **Data Selector**: `data`
- **Error Handling**: Retries on status code 400 with a maximum of 3 retries and a backoff factor of 2

## Error Handling

- **Retry Logic**: Retries on HTTP status code 400
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with fields such as `id`, `address`, `latitude`, `longitude`, and other property-related attributes. All fields are generalized and do not contain any proprietary or branded terms.