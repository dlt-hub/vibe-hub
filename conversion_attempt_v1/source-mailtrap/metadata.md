# Overview

This document provides a configuration guide for integrating with a third-party email testing API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various sync types and configurations.

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
            "name": "email_tests",
            "endpoint": {
                "path": "/v1/email_tests",
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

- **Resource Name**: `email_tests`
- **Endpoint Path and Method**: `/v1/email_tests`, default method is GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.

# Schema

- **Fields**: Use neutral field names such as `email_test_id`, `status`, `created_at`.
- **Types and Nullability**: Define field types and nullability based on the API's response schema, ensuring consistency and accuracy.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party email testing API using dltHub's REST API source. It abstracts all proprietary details, focusing on reusable patterns and configurations.