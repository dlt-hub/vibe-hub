# Overview

This document provides a configuration guide for integrating with a third-party recreation API using dltHub's REST API source format. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

## Configuration Example

Below is a sample configuration for setting up the API source using dltHub's `rest_api_source`:

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
            "name": "recreation_data",
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

The API requires an API key for authentication. The key should be included in the query parameters of each request.

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

## Resources

- **Resource Name**: `recreation_data`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

The integration includes error handling mechanisms to manage common HTTP errors and ensure reliable data extraction.

- **Retry Logic**: The system will retry requests on receiving specific HTTP status codes.
- **Backoff Strategy**: Exponential backoff is applied to manage request retries.
- **Handled Status Codes**: 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), 504 (Gateway Timeout).

## Schema

The schema for the data extracted from the API is defined with neutral field names to ensure compatibility and ease of use.

- **Fields**: The schema includes fields such as `id`, `name`, `description`, etc.
- **Types**: Each field is assigned a data type, such as string, integer, or boolean.
- **Nullability**: Fields may be nullable, depending on the API's data structure.

This configuration guide provides a clean and vendor-neutral approach to integrating with a third-party recreation API using dltHub's REST API source. By following the outlined steps, users can efficiently set up and manage their data synchronization processes.