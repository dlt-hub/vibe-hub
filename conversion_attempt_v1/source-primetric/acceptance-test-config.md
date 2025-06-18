# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports full refresh sync types and is designed to handle data retrieval from various resources offered by the API.

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
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

# Resources

- **Resource Name**: Abstracted to a neutral name such as `resource_name`.
- **Endpoint Path and Method**: The endpoint path is `/v1/resource` and the default HTTP method is `GET`.
- **Pagination Strategy**: Uses an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data is selected from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP response codes to ensure robust data retrieval.

# Schema

- **Fields, Types, Nullability**: Use neutral field names such as `analytics_id` instead of any branded identifiers. Ensure that the schema is consistent with the API's response structure and is generalized for public use.

This configuration guide ensures a clean and vendor-neutral integration with a third-party analytics API, following dltHub's REST API source format and terminology.