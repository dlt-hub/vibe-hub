# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source. The integration supports full refresh sync types, allowing for the retrieval of data from the API in a structured manner.

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
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

# Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource` using the GET method by default.
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Fallback/Handling Behavior**: Use exponential backoff for retries to manage rate limits and server errors effectively.

# Schema

- **Fields**: Use neutral field names such as `id`, `name`, `value`, etc.
- **Types and Nullability**: Define field types and nullability based on the API's response schema, ensuring consistency and accuracy in data representation.

This configuration provides a clean and generalized approach to integrating with a third-party analytics API, ensuring compatibility with dltHub's REST API source format while abstracting away any proprietary or branded content.