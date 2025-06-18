# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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
- **Configuration**: 
  - `api_key` is stored securely using `dlt.secrets["api_key"]`
  - The API key is included in the query parameters of each request

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: The data is extracted from the `data` field in the response

## Error Handling

- **Retry Logic**: 
  - Retries are configured for HTTP status codes 429, 500, 502, 503, and 504
  - Maximum of 3 retries with an exponential backoff factor of 2

## Schema

- **Fields**: 
  - `id`: Primary key for deduplication and merging
  - Additional fields should be defined based on the API's response structure, using neutral names such as `customer_id` or `user_key` where applicable

This guide ensures a clean and efficient integration with a third-party analytics API, abstracting away any proprietary or branded content for a generalized and reusable configuration.