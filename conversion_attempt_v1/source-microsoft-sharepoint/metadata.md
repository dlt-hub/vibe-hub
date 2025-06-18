# Overview

This document provides a vendor-neutral configuration for integrating with a third-party file-based API. The integration supports data synchronization from a file-based source, allowing for efficient data extraction and processing.

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
            "name": "file_resource",
            "endpoint": {
                "path": "/v1/files",
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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

## Resources

- **Resource Name**: `file_resource`
- **Endpoint Path**: `/v1/files`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use an exponential backoff factor of 2 with a maximum of 3 retries.

## Schema

- **Fields**: Use neutral field names such as `file_id`, `file_name`, and `file_size`.
- **Types**: Ensure data types are consistent with the source API's response.
- **Nullability**: Define fields as nullable based on the source API's schema.

This configuration provides a clean and generalized setup for integrating with a file-based API, ensuring compatibility with dltHub's REST API source format.