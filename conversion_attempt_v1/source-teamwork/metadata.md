# Overview

This document provides a guide for integrating with a third-party project management API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various synchronization types as defined by the API's capabilities.

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

The API requires an API key for authentication. The key should be included in the query parameters of each request.

- **Type**: `api_key`
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

# Resources

- **Resource Name**: A generic name representing the data being accessed.
- **Endpoint Path and Method**: The API endpoint path is `/v1/resource`, accessed using the GET method.
- **Pagination Strategy**: Uses an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

The integration includes error handling mechanisms to manage common HTTP errors and ensure reliable data extraction.

- **Retry/Backoff Logic**: The system will retry requests on specific HTTP status codes, such as 429 (Too Many Requests) and 5xx server errors, with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: Handles standard HTTP response codes to manage retries and error logging.

# Schema

The schema for the data extracted from the API is defined with neutral field names to ensure compatibility and ease of use.

- **Fields**: Includes fields such as `id`, `name`, `status`, etc.
- **Types**: Data types are consistent with the API's response structure.
- **Nullability**: Fields are marked as nullable based on the API's data model.

This guide provides a clean and vendor-neutral approach to integrating with a third-party project management API using dltHub's REST API source, ensuring a seamless and efficient data extraction process.