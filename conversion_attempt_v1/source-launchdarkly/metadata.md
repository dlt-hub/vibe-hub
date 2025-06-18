# Overview

This document provides a vendor-neutral configuration for integrating with a third-party feature management API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

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
            "name": "feature_flags",
            "endpoint": {
                "path": "/v1/feature_flags",
                "data_selector": "items",
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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Resource Name**: `feature_flags`
- **Endpoint Path**: `/v1/feature_flags`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `items` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `name`, and `status`, which are common in feature management APIs.
- **Types and Nullability**: Fields are typed according to their expected data types, with nullability specified where applicable.

This configuration provides a clean and generalized setup for integrating with a third-party feature management API using dltHub's REST API source, ensuring efficient data synchronization and management.