# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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

The integration uses API key-based authentication. The API key is stored securely and passed as a query parameter in each request.

- **Type**: `api_key`
- **Secrets**: `api_key` (stored in dlt.secrets)

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

## Error Handling

The integration includes robust error handling mechanisms to ensure reliable data extraction:

- **Retry Logic**: Automatically retries requests on specific HTTP status codes (e.g., 429, 500, 502, 503, 504)
- **Backoff Strategy**: Exponential backoff with a factor of 2
- **Max Retries**: 3 attempts before failing

## Schema

The schema is designed to be flexible and neutral, using generic field names to ensure compatibility across different data sources. Fields include:

- **Primary Key**: `id`
- **Field Types**: String, Integer, Boolean, etc.
- **Nullability**: Fields may be nullable depending on the data source

This guide ensures a clean and efficient integration with a third-party analytics API, following dltHub's REST API source format and best practices.