# Overview

This document provides a configuration guide for integrating with a third-party e-commerce API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various synchronization types and configurations.

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

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors, with a backoff strategy.
- **HTTP Status Codes**: The system retries on status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Max Retries**: 3 attempts with an exponential backoff factor of 2.

## Schema

- **Fields**: The schema includes fields with neutral names, such as `id`, `name`, and `created_at`.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring compatibility and data integrity.

This configuration provides a clean and vendor-neutral setup for integrating with a third-party e-commerce API using dltHub's REST API source, ensuring efficient data extraction and synchronization.