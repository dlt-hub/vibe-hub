# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data synchronization types, including full refresh and incremental updates, ensuring robust data handling and error management.

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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors, with a backoff strategy to manage rate limits and server errors.
- **HTTP Status Codes**: The system is configured to retry on status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Max Retries**: Up to 3 retries are attempted with an exponential backoff factor of 2.

## Schema

- **Fields**: The schema is defined with neutral field names to ensure compatibility and avoid proprietary terms.
- **Types and Nullability**: Each field's type and nullability are specified based on the API's response structure, ensuring accurate data representation.

This guide ensures a clean and efficient integration with a third-party analytics API, leveraging dltHub's capabilities for seamless data synchronization and error management.