# Overview

This document provides a configuration guide for integrating with a third-party forms API using dltHub's REST API source. The integration allows for data extraction from the forms service, supporting various sync types as defined by the API's capabilities.

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
            "name": "form_responses",
            "endpoint": {
                "path": "/v1/forms/responses",
                "data_selector": "responses",
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

- **Resource Name**: `form_responses`
- **Endpoint Path and Method**: `/v1/forms/responses`, default method is GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: `responses`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.

# Schema

- **Fields**: Use neutral field names such as `form_id`, `response_id`, `submitted_at`.
- **Types and Nullability**: Define fields based on the API's response structure, ensuring consistency with the data types and nullability as provided by the API.

This configuration provides a clean, vendor-neutral setup for integrating with a forms API using dltHub's REST API source, ensuring a seamless data extraction process.