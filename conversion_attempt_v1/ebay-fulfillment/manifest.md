# Overview

This document provides a configuration example for integrating with a third-party e-commerce API using dltHub's REST API source format. The integration allows for the retrieval of order data, supporting incremental synchronization based on modification dates.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": dlt.secrets["token_url"]
        }
    },
    "resource_defaults": {
        "primary_key": "order_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "orders",
            "endpoint": {
                "path": "/v1/orders",
                "data_selector": "orders",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets**:
  - `client_id`: Your client ID for the API.
  - `client_secret`: Your client secret for the API.
  - `token_url`: The URL to obtain the access token.

## Resources

- **Resource Name**: orders
- **Endpoint Path**: `/v1/orders`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 200
  - **Offset Parameter**: `offset`
- **Data Selector**: `orders`

## Error Handling

- **Retry Logic**:
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

The schema for the `orders` resource includes fields such as `order_id`, `last_modified_date`, and other order-related details. All fields are generalized and do not include any proprietary or branded terms. The schema supports nullability and various data types, ensuring flexibility in data handling.