# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various synchronization types, including full refresh and incremental updates, allowing for flexible data retrieval from the API.

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

- **Type**: API Key
- **Secrets or Config Values**: Use `api_key` stored securely in dltHub's secrets management.

# Resources

- **Resource Name**: Abstracted to a neutral name such as `resource_name`.
- **Endpoint Path and Method**: The path is `/v1/resource` with the default HTTP method being GET.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is selected from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP errors gracefully to ensure robust data retrieval.

# Schema

- **Fields, Types, Nullability**: Use neutral field names such as `analytics_id` instead of any branded identifiers. Ensure the schema is consistent with the API's response structure and is generalized for public use.

This configuration ensures a clean and reusable integration pattern for accessing a third-party analytics API, abstracting away any proprietary or branded content.