# Overview

This document provides a vendor-neutral configuration for integrating with a third-party document management API using dltHub's REST API source format. The integration supports data synchronization through RESTful endpoints, allowing for efficient data retrieval and management.

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
            "name": "documents",
            "endpoint": {
                "path": "/v1/documents",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. It is included in the query parameters of each request.

## Resources

- **Resource Name**: `documents`
- **Endpoint Path**: `/v1/documents`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `created_at`, and `updated_at`.
- **Types**: Fields are typed according to their data (e.g., string, integer, datetime).
- **Nullability**: Fields may be nullable depending on the API's response structure.

This configuration provides a clean and generalized setup for integrating with a document management API, ensuring compatibility with dltHub's REST API source format while abstracting away any proprietary or branded content.