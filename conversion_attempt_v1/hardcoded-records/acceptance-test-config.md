# Overview

This document provides a vendor-neutral configuration for integrating with a third-party API using dltHub's REST API source. The integration supports full data synchronization from the API, with a focus on clean and efficient data retrieval.

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

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource`, default method is `GET`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle HTTP status codes such as 429, 500, 502, 503, and 504 with retries.
- **Fallback/Handling Behavior**: Use a maximum of 3 retries with a backoff factor of 2.

# Schema

- **Fields**: Use neutral field names such as `id`, `name`, `value`, etc.
- **Types and Nullability**: Define fields with appropriate data types and specify if they are nullable.

This configuration ensures a clean and efficient integration with the third-party API, abstracting away any proprietary or branded content for a generalized and reusable setup.