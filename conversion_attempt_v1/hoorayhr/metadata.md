# Overview

This document provides a configuration guide for integrating with a third-party human resources API using dltHub's REST API source. The integration allows for data extraction from the API, supporting various synchronization types as defined by the API's capabilities.

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
- **Secrets or Config Values**: Use `api_key` stored securely in `dlt.secrets`.

# Resources

- **Resource Name**: Abstracted to a neutral name such as `resource_name`.
- **Endpoint Path and Method**: The path is `/v1/resource` with the default HTTP method being GET.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP errors with appropriate retry mechanisms.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and server errors.

# Schema

- **Fields, Types, Nullability**: Define fields using neutral names such as `customer_id` or `user_key` to ensure general applicability.
- **Use Neutral Field Names**: Avoid using proprietary or branded field names, ensuring the schema remains vendor-neutral and adaptable to various APIs.