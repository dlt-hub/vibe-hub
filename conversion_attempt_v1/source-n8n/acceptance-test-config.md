# Overview

This document provides a guide for integrating with a third-party analytics API using a REST API source configuration. The integration supports full refresh sync types, allowing for the retrieval of data from the API in a comprehensive manner.

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
- **Secrets or Config Values**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

# Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: The API endpoint is accessed via the path `/v1/resource` using the default HTTP GET method.
- **Pagination Strategy**: The API uses an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data returned from the API is located under the `data` field in the response.

# Error Handling

- **Retry/Backoff Logic**: The configuration includes retry logic for handling transient errors. The system will retry requests on specific HTTP status codes such as 429 (Too Many Requests) and 5xx server errors.
- **Known HTTP Response Codes**: The integration is designed to handle standard HTTP response codes, ensuring robust error management and data integrity.

# Schema

- **Fields, Types, Nullability**: The schema is defined based on the API's response structure. Field names are generalized to maintain neutrality, such as using `analytics_id` instead of any branded identifiers. The schema ensures that all fields are appropriately typed and nullable as per the API's specifications.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API, ensuring compatibility with dltHub's REST API source format.