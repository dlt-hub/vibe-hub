# Overview

This document provides a vendor-neutral configuration for integrating with a third-party task management API using dltHub's REST API source. The integration supports full data synchronization from the API, with a focus on handling resources such as spaces, folders, lists, and tasks.

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
            "name": "resource_name",
            "endpoint": {
                "path": "/v1/resource",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

# Resources

- **Resource Names**: Abstracted as `space`, `folder`, `list`, `task`.
- **Endpoint Path**: Each resource is accessed via a generalized path such as `/v1/resource`.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the response using a `data` field path.

# Error Handling

- **Retry Logic**: Configured to retry on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Implements a backoff factor of 2 with a maximum of 3 retries.
- **Timeouts**: Generalized handling for timeouts and data limits.

# Schema

- **Fields**: Includes neutral field names such as `id`, `name`, `status`.
- **Types**: Fields are typed according to their expected data types (e.g., string, integer).
- **Nullability**: Fields are marked as nullable where applicable.

This configuration ensures a clean and efficient integration with the task management API, abstracting away any proprietary or branded content for a seamless experience with dltHub.