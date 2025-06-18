# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports full data synchronization from the API, with a focus on handling dynamic data changes efficiently.

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
            "name": "resource_name",
            "endpoint": {
                "path": "/v1/resource",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors, with a backoff strategy.
- **HTTP Status Codes**: The system is configured to retry on common server error codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: Up to 3 retries are attempted for recoverable errors.
- **Backoff Factor**: A backoff factor of 2 is used to progressively increase the wait time between retries.

## Schema

- **Fields**: The schema is defined with neutral field names, ensuring compatibility with various data structures.
- **Types and Nullability**: Each field's type and nullability are configured based on the API's response structure, ensuring accurate data representation.

This configuration ensures a robust and flexible integration with the third-party analytics API, leveraging dltHub's capabilities for efficient data synchronization and error handling.