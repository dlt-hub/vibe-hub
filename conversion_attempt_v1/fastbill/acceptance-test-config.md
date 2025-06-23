# Overview

This document provides a configuration guide for integrating with a third-party billing API using dltHub's REST API source. The integration supports full refresh syncs to retrieve data from various resources such as products, recurring invoices, and revenues.

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
            "name": "recurring_invoices",
            "endpoint": {
                "path": "/v1/recurring_invoices",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "revenues",
            "endpoint": {
                "path": "/v1/revenues",
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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

### Products
- **Endpoint Path**: `/v1/products`
- **Pagination**: Offset-based with a limit of 100 records per request
- **Data Selector**: `data`

### Recurring Invoices
- **Endpoint Path**: `/v1/recurring_invoices`
- **Pagination**: Offset-based with a limit of 100 records per request
- **Data Selector**: `data`

### Revenues
- **Endpoint Path**: `/v1/revenues`
- **Pagination**: Offset-based with a limit of 100 records per request
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

The schema for each resource includes fields that are neutral and generalized. For example, identifiers are referred to as `id`, and other fields are named according to their function within the resource, such as `product_name`, `invoice_date`, or `revenue_amount`. All fields are defined based on the data structure provided by the API.