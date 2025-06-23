# Overview

This document provides a vendor-neutral configuration for integrating with a third-party exchange rates API using dltHub's REST API source format. The integration supports full refresh sync types, allowing for the retrieval of exchange rate data from the API.

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
            "name": "exchange_rates",
            "endpoint": {
                "path": "/v1/exchange_rates",
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

- **Resource Name**: `exchange_rates`
- **Endpoint Path**: `/v1/exchange_rates`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `rate`, and `currency_code`.
- **Types**: Ensure that field types are consistent with the API's response.
- **Nullability**: Fields are nullable based on the API's data structure.

This configuration provides a clean and generalized setup for accessing exchange rate data from a third-party API using dltHub's REST API source. It abstracts away any proprietary or branded content, ensuring a vendor-neutral approach.