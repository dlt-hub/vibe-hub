# Overview

This document provides a vendor-neutral guide for integrating with a third-party public API using dltHub's REST API source format. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and transformation.

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
- **HTTP Method**: `GET` (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries.

## Schema

- **Fields**: The schema is defined with neutral field names, ensuring compatibility and avoiding proprietary terms. For example, use `analytics_id` instead of any branded identifiers.
- **Types and Nullability**: The schema specifies the data types and nullability of each field, ensuring data integrity and consistency.

This guide ensures a clean and generalized approach to integrating with public APIs using dltHub, abstracting away any proprietary or branded content.