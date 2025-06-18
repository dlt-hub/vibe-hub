# Overview

This document provides a configuration guide for integrating with a third-party file storage API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various sync types and configurations.

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
            "name": "file_storage_resource",
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

- **Resource Name**: `file_storage_resource`
- **Endpoint Path and Method**: `/v1/resource`, using the default HTTP method GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Fallback/Handling Behavior**: Configure retries with a maximum of 3 attempts and a backoff factor of 2.

# Schema

- **Fields**: Use neutral field names such as `file_id`, `file_name`, `created_at`, etc.
- **Types and Nullability**: Define field types and nullability based on the API's response schema, ensuring consistency and accuracy in data representation.

This configuration guide abstracts the integration process, providing a clean and vendor-neutral setup for connecting to a third-party file storage API using dltHub's REST API source.