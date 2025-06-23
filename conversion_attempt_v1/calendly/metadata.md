# Overview

This document provides a configuration guide for integrating with a third-party scheduling API using dltHub's REST API source format. The integration allows for seamless data extraction from the API, supporting various synchronization types and authentication methods.

## Configuration Example

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

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use neutral keys such as `api_key` stored securely in `dlt.secrets`.

## Resources

- **Resource Name**: Abstracted to a neutral name like `resource_name`.
- **Endpoint Path and Method**: `/v1/resource` with the default HTTP method as GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP errors gracefully with appropriate retry mechanisms.

## Schema

- **Fields, Types, Nullability**: Use neutral field names such as `id`, `name`, and `date` to represent the data schema. Ensure consistency with the API's response structure while maintaining a vendor-neutral approach.

This configuration guide abstracts the integration details to ensure compatibility with dltHub's REST API source format, providing a clean and vendor-neutral setup for data extraction from a third-party scheduling API.