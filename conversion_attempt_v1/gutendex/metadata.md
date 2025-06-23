# Overview

This document provides a configuration guide for integrating with a third-party API using dltHub's REST API source. The integration supports data synchronization from a public domain book catalog API, allowing users to fetch and manage book metadata efficiently.

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
            "name": "book_metadata",
            "endpoint": {
                "path": "/v1/books",
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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely manage your API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `book_metadata`
- **Endpoint Path**: `/v1/books`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration will retry requests on encountering HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2, up to a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `title`, `author`, and `publication_date`.
- **Types**: Ensure all fields are appropriately typed (e.g., `id` as integer, `title` as string).
- **Nullability**: Fields may be nullable depending on the API's response structure.

This configuration provides a clean, vendor-neutral setup for integrating with a public domain book catalog API using dltHub's REST API source. Adjust the base URL, authentication details, and resource configurations as needed to match your specific API requirements.