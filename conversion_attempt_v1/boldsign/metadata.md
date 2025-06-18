# Overview

This document provides a configuration guide for integrating with a third-party document signing API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various synchronization types and authentication methods.

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
            "name": "document_signatures",
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

# Authentication

- **Type**: API Key
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Resource Name**: document_signatures
- **Endpoint Path**: `/v1/documents`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

# Schema

- **Fields**: The schema includes fields such as `id`, `document_name`, `status`, and `created_at`.
- **Types**: Fields are typed according to their expected data types (e.g., string, integer, date).
- **Nullability**: Fields are marked as nullable based on the API's response structure.

This configuration provides a clean and vendor-neutral setup for integrating with a document signing API using dltHub's REST API source. It abstracts away any proprietary or branded content, ensuring a generic and reusable integration pattern.