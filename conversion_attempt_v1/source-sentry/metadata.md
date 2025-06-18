# Overview

This document provides a generalized configuration for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from the API, abstracting away any vendor-specific details to ensure a clean and reusable setup.

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
- **Secrets or Config Values**: Use `api_key` stored securely in dlt's secrets management system.

# Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource`, default method is GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.

# Schema

- **Fields**: Use neutral field names such as `analytics_id` instead of any vendor-specific identifiers.
- **Types and Nullability**: Ensure fields are defined with appropriate data types and nullability based on the API's response schema.

This configuration provides a clean and vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format. It abstracts away any proprietary details, ensuring a reusable and open-source-friendly configuration.