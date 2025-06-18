# Overview

This document provides a configuration guide for integrating with a generic file storage API using dltHub's REST API source format. The integration supports file transfer operations and is designed to be vendor-neutral, allowing for seamless data extraction from a third-party file storage service.

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
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `file_resource`
- **Endpoint Path and Method**: The resource is accessed via the `/v1/files` endpoint using the default HTTP GET method.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry/Backoff Logic**: The configuration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: The integration is designed to handle common HTTP response codes and implement appropriate retry mechanisms.

## Schema

- **Fields, Types, Nullability**: The schema is designed to be flexible and neutral, using generic field names such as `file_id`, `file_name`, and `file_size` to represent the data structure. The schema supports various data types and handles nullability as per the API's response structure.

This configuration guide provides a clean and generalized approach to integrating with a file storage API using dltHub's REST API source format, ensuring compatibility and ease of use across different environments.