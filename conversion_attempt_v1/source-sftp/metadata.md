# Overview

This document provides a configuration guide for integrating with a generic file-based API using the dltHub REST API source. The integration supports file transfer over a secure protocol, allowing for the retrieval of data from a remote server.

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

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored securely in `dlt.secrets`.

# Resources

- **Resource Name**: `file_resource`
- **Endpoint Path and Method**: `/v1/files`, default method is `GET`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout) with retries.
- **Fallback/Handling Behavior**: Implement fallback mechanisms for handling timeouts or data-limit messages.

# Schema

- **Fields**: Use neutral field names such as `file_id`, `file_name`, `file_size`.
- **Types and Nullability**: Define field types and nullability based on the data structure provided by the API.

This configuration provides a clean, vendor-neutral setup for integrating with a file-based API using dltHub's REST API source, ensuring a seamless and efficient data retrieval process.