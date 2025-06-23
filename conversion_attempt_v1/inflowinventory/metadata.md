# Overview

This document provides a guide for integrating with a third-party inventory management API using dltHub's REST API source format. The integration allows for seamless data extraction from the API, supporting various synchronization types and configurations.

# Configuration Example

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
            "name": "inventory_data",
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

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored securely in `dlt.secrets`.

# Resources

- **Resource Name**: `inventory_data`
- **Endpoint Path and Method**: `/v1/resource`, default method is GET.
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error responses with appropriate retry mechanisms.

# Schema

- **Fields**: Use neutral field names such as `inventory_id`, `product_name`, `quantity`, etc.
- **Types and Nullability**: Define field types and nullability based on the API's response structure, ensuring consistency and accuracy in data representation.

This guide ensures a clean and vendor-neutral integration with the third-party API, adhering to dltHub's REST API source format and best practices.