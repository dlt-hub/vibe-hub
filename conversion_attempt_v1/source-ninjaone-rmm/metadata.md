# Overview

This document provides a vendor-neutral configuration for integrating with a third-party remote monitoring and management (RMM) API. The integration supports data synchronization through a REST API, allowing for efficient data retrieval and management.

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

- **Type**: API Key
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. It is included in the query parameters of each request.

## Resources

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields with neutral names such as `id`, `name`, and `status`.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring compatibility and data integrity.

This configuration provides a clean and generalized approach to integrating with a third-party RMM API, ensuring flexibility and ease of use within the dltHub framework.