# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The integration supports both full refresh and incremental sync types, allowing for flexible data retrieval from the API.

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
- **Secrets or Config Values**: The API key is stored securely using `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

# Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: The resource is accessed via the `/v1/resource` path using the default HTTP GET method.
- **Pagination Strategy**: The API uses an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data returned by the API is located under the `data` field in the response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors, such as HTTP status codes 429, 500, 502, 503, and 504. The retry mechanism includes a maximum of 3 retries with an exponential backoff factor of 2.
- **Known HTTP Response Codes**: The integration is designed to handle common HTTP response codes, ensuring robust error management.

# Schema

- **Fields, Types, Nullability**: The schema is defined based on the API's response structure. Field names are generalized to maintain neutrality, such as using `analytics_id` instead of any proprietary identifiers. The schema ensures compatibility with the data types and nullability as specified by the API.