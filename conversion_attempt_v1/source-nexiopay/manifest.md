# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data resources, each with specific endpoints and configurations. The API allows for data retrieval through HTTP GET requests, with support for pagination and incremental synchronization based on datetime fields.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "terminal_list",
            "endpoint": {
                "path": "/pay/v3/getTerminalList",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "recipients",
            "endpoint": {
                "path": "/payout/v3/recipient",
                "data_selector": "rows",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "payment_types",
            "endpoint": {
                "path": "/transaction/v3/paymentTypes",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "user",
            "endpoint": {
                "path": "/user/v3/account/whoAmI",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "spendbacks",
            "endpoint": {
                "path": "/payout/v3/spendback",
                "data_selector": "rows",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "card_tokens",
            "endpoint": {
                "path": "/card/v3",
                "data_selector": "rows",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: The API username.
  - `api_key`: The API key used as a password.

## Resources

### Terminal List
- **Endpoint Path**: `/pay/v3/getTerminalList`
- **Primary Key**: `terminalId`
- **Pagination**: Single page
- **Data Selector**: `data`

### Recipients
- **Endpoint Path**: `/payout/v3/recipient`
- **Primary Key**: `recipientId`
- **Pagination**: Offset-based
  - **Limit**: 10
  - **Offset Parameter**: `offset`
- **Data Selector**: `rows`

### Payment Types
- **Endpoint Path**: `/transaction/v3/paymentTypes`
- **Primary Key**: `id`
- **Pagination**: Offset-based
  - **Limit**: 10
  - **Offset Parameter**: `offset`
- **Data Selector**: `data`

### User
- **Endpoint Path**: `/user/v3/account/whoAmI`
- **Primary Key**: `accountId`
- **Pagination**: Single page
- **Data Selector**: `data`

### Spendbacks
- **Endpoint Path**: `/payout/v3/spendback`
- **Primary Key**: `id`
- **Pagination**: Offset-based
  - **Limit**: 10
  - **Offset Parameter**: `offset`
- **Data Selector**: `rows`

### Card Tokens
- **Endpoint Path**: `/card/v3`
- **Primary Key**: `key`
- **Pagination**: Offset-based
  - **Limit**: 10
  - **Offset Parameter**: `offset`
- **Data Selector**: `rows`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a defined schema with fields, types, and nullability. The schemas are consistent with the data structure provided by the API and use neutral field names. For example, `terminalId`, `recipientId`, `id`, `accountId`, `key`, etc., are used as primary keys for their respective resources.