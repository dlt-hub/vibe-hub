# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various synchronization types, including full refresh and incremental updates, while handling potential errors and pagination.

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
- **Endpoint Path and Method**: `/v1/resource`, default method is `GET`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP errors gracefully to ensure robust data synchronization.

# Schema

- **Fields**: Use neutral field names such as `customer_id` or `user_key` to represent unique identifiers.
- **Types and Nullability**: Ensure that field types and nullability are consistent with the API's response schema, using generalized field names.