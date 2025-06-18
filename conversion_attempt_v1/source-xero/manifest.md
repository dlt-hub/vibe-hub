# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and configuration settings. The API uses OAuth2 and Bearer token authentication methods to access data.

# Configuration Example

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
            "token_url": "https://identity.example.com/connect/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "bank_transactions",
            "endpoint": {
                "path": "/v1/bank_transactions",
                "data_selector": "BankTransactions",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "page_size"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 403, 429],
                    "max_retries": 3,
                    "backoff_factor": 30
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "Contacts",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "page_size"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 403, 429],
                    "max_retries": 3,
                    "backoff_factor": 30
                }
            }
        },
        // Additional resources can be added here following the same structure
    ]
})
```

# Authentication

The API supports two types of authentication:

1. **OAuth2 Authentication**: Requires `client_id`, `client_secret`, and `token_url`.
2. **Bearer Token Authentication**: Requires an `access_token`.

Secrets or configuration values should be stored securely and accessed using `dlt.secrets`.

# Resources

Each resource is defined with the following parameters:

- **Resource Name**: A unique identifier for the resource.
- **Endpoint Path**: The API path to access the resource.
- **Data Selector**: The JSON path to extract data from the API response.
- **Pagination Strategy**: Uses a page number strategy with configurable `page_param` and `limit_param`.
- **Error Handling**: Configures retry logic for specific HTTP status codes with a defined backoff strategy.

# Error Handling

The error handling configuration includes:

- **Retry Logic**: Retries on HTTP status codes 401, 403, and 429.
- **Backoff Strategy**: Uses a constant backoff factor of 30 seconds.
- **Max Retries**: Limits the number of retries to 3 attempts.

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to maintain neutrality. For example, `BankTransactionID` is used as a primary key for the `bank_transactions` resource.