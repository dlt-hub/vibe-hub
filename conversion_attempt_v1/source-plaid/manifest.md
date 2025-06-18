# Overview

This document provides a configuration guide for integrating with a third-party financial API using dltHub's REST API source format. The integration allows for the retrieval of account balances and transaction data. The API supports both full and incremental synchronization of data.

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
            "name": "balance",
            "endpoint": {
                "path": "/v1/accounts/balance",
                "data_selector": "accounts.*",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "transaction",
            "endpoint": {
                "path": "/v1/transactions",
                "data_selector": "transactions.*",
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

- **Type**: `api_key`
- **Secrets**: 
  - `api_key`: The API key for accessing the service.
  - `client_id`: The client identifier.
  - `access_token`: The access token for user-specific data.

## Resources

### Balance

- **Resource Name**: `balance`
- **Endpoint Path**: `/v1/accounts/balance`
- **HTTP Method**: `POST`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `accounts.*`
- **Primary Key**: `account_id`

### Transaction

- **Resource Name**: `transaction`
- **Endpoint Path**: `/v1/transactions`
- **HTTP Method**: `POST`
- **Pagination Strategy**: `offset`
  - **Limit**: 100
  - **Offset Parameter**: `offset`
- **Data Selector**: `transactions.*`
- **Primary Key**: `transaction_id`
- **Incremental Sync**: Based on `date` field

## Error Handling

- **Retry Logic**: Implement retry on common HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Balance Schema

- **Fields**:
  - `account_id`: string
  - `available`: nullable number
  - `current`: number
  - `iso_currency_code`: nullable string
  - `limit`: nullable number
  - `unofficial_currency_code`: nullable string

### Transaction Schema

- **Fields**:
  - `account_id`: string
  - `account_owner`: nullable string
  - `amount`: number
  - `authorized_date`: nullable string
  - `authorized_datetime`: nullable string
  - `category`: array of strings
  - `category_id`: nullable string
  - `check_number`: nullable string
  - `date`: string
  - `datetime`: nullable string
  - `iso_currency_code`: string
  - `location`: object with nullable fields
  - `merchant_name`: nullable string
  - `name`: string
  - `payment_channel`: nullable string
  - `payment_meta`: object with nullable fields
  - `pending`: nullable boolean
  - `pending_transaction_id`: nullable string
  - `personal_finance_category`: nullable string
  - `transaction_code`: nullable string
  - `transaction_id`: string
  - `transaction_type`: string
  - `unofficial_currency_code`: nullable string

This configuration provides a clean and generalized setup for integrating with a financial API using dltHub's REST API source, ensuring a vendor-neutral approach.