# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various data synchronization types, including full refresh and incremental updates, allowing for flexible data retrieval and management.

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

The integration uses API key authentication. The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request to authenticate API calls.

# Resources

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path**: `/v1/resource` is the generalized path for accessing the resource.
- **HTTP Method**: The default method used is GET.
- **Pagination Strategy**: Offset pagination is employed, with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

# Error Handling

The configuration includes error handling mechanisms to manage common HTTP errors:

- **Retry Logic**: The system retries requests on receiving status codes 429, 500, 502, 503, and 504.
- **Max Retries**: Up to 3 retries are attempted.
- **Backoff Factor**: A backoff factor of 2 is used to space out retries.

# Schema

The schema defines the structure of the data fields, ensuring consistency and neutrality. Field names are generalized to avoid any proprietary references, such as using `analytics_id` instead of specific identifiers. The schema supports various data types and nullability as per the API's response structure.