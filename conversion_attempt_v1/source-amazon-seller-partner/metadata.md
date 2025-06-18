# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various data resources and utilizes API key-based authentication for secure access. The API offers multiple endpoints for retrieving data, with support for pagination and error handling.

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
            "name": "orders",
            "endpoint": {
                "path": "/v1/orders",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "order_items",
            "endpoint": {
                "path": "/v1/order_items",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "financial_events",
            "endpoint": {
                "path": "/v1/financial_events",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "financial_event_groups",
            "endpoint": {
                "path": "/v1/financial_event_groups",
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

- **Type**: API Key
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Orders**
  - **Endpoint Path**: `/v1/orders`
  - **Pagination**: Offset-based with a limit of 100 records per request
  - **Data Selector**: `data`

- **Order Items**
  - **Endpoint Path**: `/v1/order_items`
  - **Pagination**: Offset-based with a limit of 100 records per request
  - **Data Selector**: `data`

- **Financial Events**
  - **Endpoint Path**: `/v1/financial_events`
  - **Pagination**: Offset-based with a limit of 100 records per request
  - **Data Selector**: `data`

- **Financial Event Groups**
  - **Endpoint Path**: `/v1/financial_event_groups`
  - **Pagination**: Offset-based with a limit of 100 records per request
  - **Data Selector**: `data`

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema for each resource includes fields such as `id`, `customer_id`, `event_date`, and `amount`.
- **Types**: Fields are typed according to their data (e.g., integer, string, date).
- **Nullability**: Fields may be nullable depending on the data source's specifications.

This configuration provides a robust framework for accessing and synchronizing data from a third-party analytics API using dltHub's REST API source.