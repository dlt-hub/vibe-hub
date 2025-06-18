# Overview

This document provides a configuration guide for integrating with a third-party document management API using dltHub's REST API source. The integration supports both full refresh and incremental data synchronization, allowing for efficient data retrieval and management.

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
        "primary_key": "document_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "documents",
            "endpoint": {
                "path": "/v1/documents",
                "data_selector": "documents",
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
- **Configuration**: The API key should be stored securely using `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

## Resources

- **Resource Name**: `documents`
- **Endpoint Path**: `/v1/documents`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is located under the `documents` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `document_id`, `title`, `created_at`, and `modified_at`.
- **Types**: Ensure all fields are typed appropriately, e.g., `document_id` as a string, `created_at` as a datetime.
- **Nullability**: Fields should be marked as nullable if they can be absent in the API response.

This configuration provides a robust framework for integrating with a document management API, ensuring efficient data synchronization and error handling.