# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source. The integration supports full refresh sync types, allowing for the retrieval of data from the API in a structured manner.

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
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access the API key required for authentication.

# Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: The API endpoint is accessed via the path `/v1/resource` using the default HTTP GET method.
- **Pagination Strategy**: Utilizes an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the response using the `data` field.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors, with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).

# Schema

- **Fields**: The schema should be defined with neutral field names, such as `customer_id` or `user_key`, to ensure compatibility and generalization.
- **Types and Nullability**: Define the data types and nullability of each field based on the API's response structure, ensuring consistency and accuracy in data representation.

This configuration provides a clean and generalized approach to integrating with a third-party analytics API, ensuring compatibility with dltHub's REST API source format while abstracting away any proprietary or branded content.