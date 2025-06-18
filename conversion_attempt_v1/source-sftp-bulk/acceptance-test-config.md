# Overview

This document provides a configuration guide for integrating with a third-party file transfer API using a RESTful approach. The integration supports data synchronization through a bulk file transfer mechanism, allowing for efficient data retrieval and processing.

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

- **Type**: API Key
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. It is included in the query parameters of each request.

## Resources

- **Resource Name**: file_transfer
- **Endpoint Path**: `/v1/files`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `files` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `file_name`, `file_size`, and `upload_date`.
- **Field Types**: All fields are defined with appropriate data types and nullability settings.
- **Neutral Field Names**: The schema uses generic field names to ensure compatibility and avoid vendor-specific terminology.

This configuration provides a robust framework for integrating with a third-party file transfer API, ensuring efficient data synchronization and error handling.