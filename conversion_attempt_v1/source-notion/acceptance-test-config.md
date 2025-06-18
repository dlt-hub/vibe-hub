# Overview

This document provides a guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data synchronization methods, including incremental and full refresh, allowing for flexible data retrieval and management.

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

The API requires authentication using an API key. The key should be included in the query parameters of each request.

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

## Resources

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data field within the response that contains the records.

## Error Handling

The integration includes error handling mechanisms to manage common HTTP errors and ensure reliable data retrieval.

- **Retry Logic**: Automatically retries requests on specific HTTP status codes.
- **Backoff Strategy**: Implements exponential backoff with a maximum of 3 retries and a backoff factor of 2.
- **Handled Status Codes**: 429, 500, 502, 503, 504

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

## Schema

The schema defines the structure of the data retrieved from the API. It includes field names, types, and nullability, using neutral terms to ensure compatibility with various data sources.

- **Fields**: Use generic field names such as `customer_id`, `timestamp`, `value`, etc.
- **Types**: Define appropriate data types for each field (e.g., string, integer, boolean).
- **Nullability**: Specify whether fields can be null based on the API's data structure.

This guide provides a clean, vendor-neutral framework for integrating with a third-party analytics API using dltHub's REST API source, ensuring a seamless and efficient data synchronization process.