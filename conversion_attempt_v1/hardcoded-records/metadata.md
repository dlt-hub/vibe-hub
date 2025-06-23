# Overview

This document provides a configuration example for integrating with a generic third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, such as countries and orders, using a RESTful approach.

# Configuration Example

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
            "name": "countries",
            "endpoint": {
                "path": "/v1/countries",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/v1/orders",
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

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of the request.

# Resources

## Countries

- **Resource Name**: countries
- **Endpoint Path**: `/v1/countries`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

## Orders

- **Resource Name**: orders
- **Endpoint Path**: `/v1/orders`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

# Error Handling

- **Retry Logic**: The integration will retry requests on encountering HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts will be made before failing.
- **Backoff Factor**: A backoff factor of 2 is used to increase the wait time between retries.

# Schema

- **Countries Resource**: Includes fields such as `country_id`, `country_name`, etc. (abstracted for general use).
- **Orders Resource**: Includes fields such as `order_id`, `order_date`, etc. (abstracted for general use).

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format. It abstracts away any proprietary or branded content, ensuring a generic and reusable integration pattern.