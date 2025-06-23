# Overview

This document provides a vendor-neutral configuration for integrating with a third-party database API using dltHub's REST API source format. The integration supports data synchronization from a database source, abstracting away any proprietary or branded content.

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
- **Secrets or Config Values**: Use neutral keys such as `api_key` for authentication purposes. The API key should be stored securely and accessed via `dlt.secrets`.

# Resources

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path and Method**: The path to access the resource is `/v1/resource`, using the default HTTP method GET.
- **Pagination Strategy**: Utilizes an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data is selected from the response using the key `data`.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors with a backoff strategy.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Fallback/Handling Behavior**: Define fallback mechanisms for handling errors and ensuring data consistency.

# Schema

- **Fields, Types, Nullability**: Define the schema with neutral field names such as `id`, `name`, `value`, etc. Ensure that the schema is consistent with the data structure provided by the API.
- **Use Neutral Field Names**: Avoid using branded or proprietary field names. Use generic terms like `customer_id` or `user_key`.

This configuration provides a clean and generalized approach to integrating with a third-party database API, ensuring compatibility with dltHub's REST API source format while maintaining a vendor-neutral stance.