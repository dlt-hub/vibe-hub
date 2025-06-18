# Overview

This document provides a configuration example for integrating with a third-party file transfer API using the dltHub REST API source. The integration supports file transfer operations and is designed to be vendor-neutral, abstracting away any proprietary or branded content.

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
            "name": "file_transfer",
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

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of the request.

# Resources

- **Resource Name**: `file_transfer`
- **Endpoint Path**: `/v1/files`
- **HTTP Method**: `GET` (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema is designed to be neutral, using generic field names such as `file_id`, `file_name`, and `file_size`.
- **Types**: Ensure that field types are consistent with the data returned by the API.
- **Nullability**: Fields are nullable unless specified otherwise by the API documentation.

This configuration provides a clean and generalized setup for integrating with a file transfer API, ensuring compatibility with dltHub's REST API source format.