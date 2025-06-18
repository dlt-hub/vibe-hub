# Overview

This document provides a vendor-neutral configuration for integrating with a third-party payment processing API using dltHub's REST API source format. The integration supports both full refresh and incremental data synchronization.

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
            "name": "subscription_data",
            "endpoint": {
                "path": "/v1/subscriptions",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

# Resources

- **Resource Name**: `subscription_data`
- **Endpoint Path**: `/v1/subscriptions`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `customer_id`, and `subscription_status`.
- **Types**: Fields are typed according to their expected data types (e.g., string, integer).
- **Nullability**: Fields may be nullable depending on the API's response structure.

This configuration provides a clean and generalized setup for integrating with a third-party API, ensuring compatibility with dltHub's REST API source format while abstracting away any proprietary or branded content.