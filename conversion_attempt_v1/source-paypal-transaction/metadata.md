# Overview

This document provides a configuration guide for integrating with a third-party payment processing API using dltHub's REST API source. The integration supports data synchronization for various resources such as transactions, balances, and payment lists. The API offers secure access through OAuth authentication and supports pagination for efficient data retrieval.

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
            "token_url": "https://api.example.com/oauth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v1/transactions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_cursor",
                    "cursor_param": "cursor"
                }
            }
        },
        {
            "name": "balances",
            "endpoint": {
                "path": "/v1/balances",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "list_payments",
            "endpoint": {
                "path": "/v1/payments",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

The API uses OAuth2 for authentication. The following credentials are required:

- `client_id`: Your application's client ID.
- `client_secret`: Your application's client secret.
- `token_url`: The URL to obtain the OAuth token.

These credentials should be securely stored and accessed using `dlt.secrets`.

## Resources

### Transactions

- **Endpoint Path**: `/v1/transactions`
- **Pagination**: Cursor-based
  - **Cursor Path**: `next_cursor`
  - **Cursor Parameter**: `cursor`
- **Data Selector**: `data`

### Balances

- **Endpoint Path**: `/v1/balances`
- **Pagination**: Single page
- **Data Selector**: `data`

### List Payments

- **Endpoint Path**: `/v1/payments`
- **Pagination**: Offset-based
  - **Limit**: 100
  - **Offset Parameter**: `offset`
- **Data Selector**: `data`

## Error Handling

The API integration includes error handling with retry logic for specific HTTP status codes. The configuration is as follows:

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

## Schema

The schema for each resource includes fields that are neutral and generalized. For example, identifiers are referred to as `id`, and timestamps are in ISO 8601 format. Ensure that field names and types are consistent with the API's response structure.