# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports both full refresh and incremental data syncs, allowing for efficient data retrieval and management.

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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

## Resources

- **Resource Name**: Abstracted to a neutral name such as `resource_name`.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: Default is `GET`.
- **Pagination Strategy**: Uses an offset-based paginator with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries.
- **Timeouts**: Configurable timeout settings ensure that requests do not hang indefinitely.

## Schema

- **Fields**: The schema is defined with neutral field names such as `customer_id` or `user_key`.
- **Types and Nullability**: Each field's type and nullability are specified based on the API's response structure.

This guide ensures a clean and efficient integration with a third-party analytics API, abstracting away any proprietary or branded content for a generalized and reusable configuration.