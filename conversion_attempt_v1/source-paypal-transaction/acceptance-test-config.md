# Overview

This document provides a configuration example for integrating with a third-party financial transactions API using dltHub's REST API source format. The integration supports various data retrieval methods, including full refresh and incremental syncs, and handles authentication via API keys.

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
            "name": "financial_transactions",
            "endpoint": {
                "path": "/v1/transactions",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "account_balances",
            "endpoint": {
                "path": "/v1/balances",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. It is included in the query parameters of each request.

# Resources

## Financial Transactions

- **Endpoint Path**: `/v1/transactions`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

## Account Balances

- **Endpoint Path**: `/v1/balances`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `transaction_id`, `amount`, `currency`, `balance_id`, and `balance_amount`.
- **Types**: Fields are typed according to their expected data types (e.g., string, integer, float).
- **Nullability**: Fields may be nullable depending on the API's response structure.

This configuration provides a robust framework for integrating with a financial transactions API, ensuring data is retrieved efficiently and securely.