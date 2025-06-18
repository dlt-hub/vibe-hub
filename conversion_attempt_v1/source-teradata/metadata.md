# Overview

This document provides a vendor-neutral configuration for integrating with a third-party database API using dltHub's REST API source. The integration supports data synchronization from the database, abstracting away any proprietary or branded content.

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
- **Secrets or Config Values**: Use neutral keys such as `api_key` stored securely in dlt's secrets management.

# Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource` using the default HTTP method GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Fallback/Handling Behavior**: Define fallback mechanisms for handling errors gracefully.

# Schema

- **Fields, Types, Nullability**: Use neutral field names such as `analytics_id` instead of any branded identifiers. Ensure the schema is consistent with the data structure provided by the API and is neutral in naming conventions.

This configuration provides a clean, generalized setup for integrating with a third-party database API using dltHub's REST API source, ensuring no proprietary or branded content is included.