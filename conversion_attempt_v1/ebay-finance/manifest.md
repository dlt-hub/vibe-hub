# Overview

This document provides a configuration example for integrating with a third-party financial API using dltHub's REST API source format. The integration supports retrieving transaction and payout data through a RESTful interface, utilizing pagination and authentication mechanisms.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["username"],
            "client_secret": dlt.secrets["password"],
            "token_url": dlt.secrets["token_refresh_endpoint"],
            "refresh_token": dlt.secrets["refresh_token"]
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
                "path": "/v1/transaction",
                "data_selector": "transactions",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "payouts",
            "endpoint": {
                "path": "/v1/payout",
                "data_selector": "payouts",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets or Config Values**:
  - `client_id`: Your API client ID
  - `client_secret`: Your API client secret
  - `token_url`: Endpoint for token refresh
  - `refresh_token`: Refresh token for obtaining new access tokens

## Resources

### Transactions

- **Endpoint Path**: `/v1/transaction`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 1000
  - **Offset Parameter**: `offset`
- **Data Selector**: `transactions`

### Payouts

- **Endpoint Path**: `/v1/payout`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 1000
  - **Offset Parameter**: `offset`
- **Data Selector**: `payouts`

## Error Handling

- **Retry/Backoff Logic**:
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

### Transactions

- **Fields**:
  - `transactionDate`: string (required)
  - `transactionId`: string
  - `amount`: object
    - `currency`: string
    - `value`: string
  - `buyer`: object
    - `username`: string
  - `orderId`: string
  - `transactionStatus`: string

### Payouts

- **Fields**:
  - `payoutId`: string (required)
  - `payoutDate`: string (required)
  - `amount`: object
    - `currency`: string
    - `value`: string
  - `payoutStatus`: string
  - `transactionCount`: number

This configuration provides a clean and generalized setup for accessing financial data through a REST API, abstracting away any vendor-specific details.