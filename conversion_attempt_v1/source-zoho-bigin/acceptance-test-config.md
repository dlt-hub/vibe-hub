# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source format. The configuration is designed to facilitate data extraction from the API, supporting both full refresh and incremental sync types.

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
- **Secrets or Config Values**: Use neutral keys such as `api_key` stored securely in `dlt.secrets`.

# Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource`, default method is GET
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Fallback/Handling Behavior**: Implement fallback mechanisms for handling API rate limits and temporary outages.

# Schema

- **Fields**: Use neutral field names such as `id`, `name`, `created_at`, etc.
- **Types and Nullability**: Define field types and nullability based on the API's response schema, ensuring consistency and data integrity.

This configuration provides a clean and generalized approach to integrating with a third-party analytics API, ensuring compatibility with dltHub's REST API source format while abstracting away any proprietary or branded content.