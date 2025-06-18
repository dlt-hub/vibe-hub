# Overview

This document provides a vendor-neutral configuration for integrating with a third-party email API using dltHub's REST API source format. The integration supports data synchronization from the API, abstracting away any proprietary or branded content.

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
            "name": "email_data",
            "endpoint": {
                "path": "/v1/emails",
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
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `email_data`
- **Endpoint Path**: `/v1/emails`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration will retry requests on encountering HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2, up to a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `subject`, `sender`, `recipient`, and `timestamp`.
- **Types**: Ensure all fields are appropriately typed (e.g., string, integer, datetime).
- **Nullability**: Define which fields can be null based on the API's response structure.

This configuration provides a clean and generalized setup for integrating with a third-party email API, ensuring compatibility with dltHub's REST API source format while abstracting away any proprietary details.