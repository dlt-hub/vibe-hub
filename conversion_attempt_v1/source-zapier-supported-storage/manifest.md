# Overview

This document provides a configuration guide for integrating with a third-party storage API using dltHub's REST API source format. The integration allows for data retrieval from a specified endpoint, supporting a simple GET request method. The configuration is designed to be vendor-neutral and does not include any proprietary or branded content.

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
            "name": "storage_records",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of the request.

# Resources

- **Resource Name**: `storage_records`
- **Endpoint Path and Method**: The resource is accessed via a GET request to the path `/v1/resource`.
- **Pagination Strategy**: The resource uses a `single_page` pagination strategy, indicating that all data is retrieved in a single request.
- **Data Selector**: The data is extracted from the response using the `data` field.

# Error Handling

- **Retry/Backoff Logic**: Not explicitly defined in the YAML, but can be configured in dltHub to handle common HTTP errors with retry logic.
- **Known HTTP Response Codes**: Standard HTTP response codes should be handled, such as 200 for success and 4xx/5xx for errors.

# Schema

- **Fields, Types, Nullability**: The schema is flexible, allowing additional properties and not enforcing strict field types. This is suitable for dynamic data structures.
- **Neutral Field Names**: The schema does not specify field names, allowing for a generic data structure.

This configuration provides a clean and generalized setup for integrating with a third-party storage API using dltHub's REST API source format. It abstracts away any vendor-specific details, focusing on the essential parameters and patterns required for the integration.