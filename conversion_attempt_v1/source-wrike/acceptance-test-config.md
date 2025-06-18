# Overview

This document provides a vendor-neutral guide for integrating with a third-party project management API using dltHub's REST API source format. The integration supports full refresh sync types, allowing for the retrieval of comprehensive data sets from the API.

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

The API requires API key-based authentication. The API key should be stored securely and referenced in the configuration as shown in the example. The key is included in the query parameters of each request.

# Resources

- **Resource Name**: A generic name representing the data being accessed.
- **Endpoint Path**: `/v1/resource` is the generalized path for accessing the resource.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the response using the "data" field.

# Error Handling

The integration includes basic error handling with retry logic for common HTTP status codes that indicate temporary issues. The configuration allows for up to three retries with an exponential backoff factor of 2.

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

# Schema

The schema for the data is defined with neutral field names and types. Each field's nullability is specified to ensure data integrity during the sync process. The primary key for deduplication and merging is set to "id".