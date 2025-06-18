# Overview

This document provides a vendor-neutral guide for integrating with a third-party file transfer API using the dltHub REST API source format. The integration supports secure file transfer operations over a network.

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
            "name": "file_transfer",
            "endpoint": {
                "path": "/v1/files",
                "data_selector": "files",
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
- **Secrets or Config Values**: Use `api_key` stored securely in `dlt.secrets`.

## Resources

- **Resource Name**: `file_transfer`
- **Endpoint Path and Method**: `/v1/files`, default method is GET
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request
- **Data Selector**: `files`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.

## Schema

- **Fields**: Use neutral field names such as `file_id`, `file_name`, `file_size`.
- **Types and Nullability**: Define fields with appropriate data types and specify if they can be null.

This guide ensures a clean and generalized approach to integrating with a third-party file transfer API using dltHub's REST API source format, abstracting away any proprietary or branded content.