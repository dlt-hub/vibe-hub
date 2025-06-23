# Overview

This document provides a guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports full refresh sync types, allowing you to retrieve data from the API in a structured and efficient manner.

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
            "name": "customer_usage",
            "endpoint": {
                "path": "/v1/customer_usage",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "wallets",
            "endpoint": {
                "path": "/v1/wallets",
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
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

# Resources

- **Resource Name**: `customer_usage`
  - **Endpoint Path**: `/v1/customer_usage`
  - **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
  - **Data Selector**: `data`

- **Resource Name**: `wallets`
  - **Endpoint Path**: `/v1/wallets`
  - **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
  - **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP errors gracefully to ensure robust data retrieval.

# Schema

- **Fields**: Use neutral field names such as `customer_id`, `transaction_id`, etc.
- **Types and Nullability**: Ensure that the schema reflects the data types and nullability as provided by the API, using generic field names.

This guide provides a clean and vendor-neutral approach to integrating with a third-party analytics API using dltHub's REST API source. Follow the configuration and guidelines to set up and manage your data integration effectively.