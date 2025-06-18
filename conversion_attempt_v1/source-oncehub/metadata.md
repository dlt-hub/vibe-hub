# Overview

This document provides a configuration guide for integrating with a third-party scheduling API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various synchronization types as defined by the API's capabilities.

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
- **Endpoint Path and Method**: `/v1/resource` (default method: GET)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Fallback/Handling Behavior**: Define fallback mechanisms for handling API rate limits and server errors.

# Schema

- **Fields**: Use neutral field names such as `customer_id`, `event_time`, and `event_type`.
- **Types and Nullability**: Ensure data types and nullability are consistent with the API's schema, using generic field names.

This configuration guide abstracts the integration process, ensuring a clean and vendor-neutral setup for accessing a third-party scheduling API using dltHub's REST API source.