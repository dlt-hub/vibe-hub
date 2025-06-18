# Overview

This document provides a vendor-neutral configuration for integrating with a third-party search API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data retrieval and management.

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
            "name": "search_results",
            "endpoint": {
                "path": "/v1/search",
                "data_selector": "hits",
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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Resource Name**: search_results
- **Endpoint Path**: `/v1/search`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `hits` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `name`, and `description`, which are extracted from the API response.
- **Field Types**: All fields are treated as strings unless specified otherwise.
- **Nullability**: Fields are nullable unless explicitly defined as required.

This configuration provides a clean and generalized setup for integrating with a third-party search API using dltHub's REST API source, ensuring efficient data synchronization and management.