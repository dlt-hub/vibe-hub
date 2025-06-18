# Overview

This document provides a configuration guide for integrating with a third-party currency exchange rates API using dltHub's REST API source. The integration allows for the retrieval of exchange rate data through a RESTful API, supporting various authentication and pagination methods.

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
- **Configuration**: The API key should be stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

# Resources

- **Resource Name**: `exchange_rates`
- **Endpoint Path**: `/v1/exchange_rates`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Fallback Behavior**: Define fallback mechanisms for handling errors, such as logging and alerting.

# Schema

- **Fields**: The schema should include fields relevant to exchange rates, such as `currency_code`, `exchange_rate`, and `timestamp`.
- **Field Types**: Ensure that field types are consistent with the data returned by the API.
- **Nullability**: Define which fields can be null based on the API's response structure.

This configuration provides a clean and generalized setup for accessing exchange rate data from a third-party API using dltHub's REST API source. It abstracts away any vendor-specific details, ensuring a vendor-neutral integration.