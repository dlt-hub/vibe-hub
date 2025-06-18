# Overview

This document provides a generalized configuration for integrating with a third-party database API using dltHub's REST API source format. The integration supports syncing data from multiple databases in a single connection, with options for incremental updates and schema discovery.

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
            "name": "database_resource",
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
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

# Resources

- **Resource Name**: `database_resource`
- **Endpoint Path and Method**: `/v1/resource`, default method is GET.
- **Pagination Strategy**: Offset pagination with a limit of 100.
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for HTTP status codes 429, 500, 502, 503, and 504.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.

# Schema

- **Fields**: Use neutral field names such as `customer_id` or `user_key`.
- **Types and Nullability**: Define fields with appropriate data types and nullability based on the API's response schema.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party database API using dltHub's REST API source format. It abstracts away any proprietary or branded content, ensuring a generic and reusable integration pattern.