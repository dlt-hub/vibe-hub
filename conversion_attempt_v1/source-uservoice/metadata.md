# Overview

This document provides a configuration guide for integrating with a third-party feedback management API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various sync types and configurations.

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
            "name": "feedback_resource",
            "endpoint": {
                "path": "/v1/feedback",
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

- **Resource Name**: `feedback_resource`
- **Endpoint Path and Method**: `/v1/feedback`, default method is `GET`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP errors gracefully with appropriate retry mechanisms.

# Schema

- **Fields**: Use neutral field names such as `feedback_id`, `customer_id`, `feedback_text`, etc.
- **Types and Nullability**: Define fields with appropriate data types and nullability based on the API's response schema.

This configuration ensures a clean and efficient integration with the feedback management API, abstracting away any vendor-specific details and focusing on a generalized, open-source compatible setup.