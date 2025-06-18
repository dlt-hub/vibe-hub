# Overview

This document provides a guide for integrating with a third-party human resources API using dltHub's REST API source format. The integration allows for seamless data extraction from the API, supporting various data synchronization types.

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

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use neutral keys such as `api_key` stored securely in `dlt.secrets`.

# Resources

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path and Method**: `/v1/resource` using the default HTTP GET method.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests) and 500-series server errors.
- **Fallback/Handling Behavior**: Define fallback mechanisms for handling API rate limits and server errors.

# Schema

- **Fields**: Use neutral field names such as `customer_id` or `user_key`.
- **Types and Nullability**: Define field types and nullability based on the API's response schema, ensuring consistency and neutrality.

This guide ensures a clean, vendor-neutral integration with a third-party human resources API, following dltHub's REST API source format and terminology.