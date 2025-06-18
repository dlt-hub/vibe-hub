# Overview

This document provides a vendor-neutral configuration for integrating with a third-party REST API using dltHub. The integration supports full refresh sync types, allowing you to retrieve data from various resources provided by the API.

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
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/v1/activities",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "unsubscribes",
            "endpoint": {
                "path": "/v1/unsubscribes",
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
- **Location**: The API key is included in the query parameters of each request.

# Resources

- **Resource Name**: `campaigns`, `activities`, `unsubscribes`
- **Endpoint Path and Method**: Each resource has a unique endpoint path (e.g., `/v1/campaigns`) and uses the GET method by default.
- **Pagination Strategy**: Uses an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests) and 500 (Internal Server Error) by retrying the request.
- **Fallback/Handling Behavior**: Define fallback mechanisms for handling unexpected errors or timeouts.

# Schema

- **Fields, Types, Nullability**: The schema for each resource should be defined based on the API's response structure, using neutral field names such as `campaign_id`, `activity_id`, and `unsubscribe_id`.
- **Consistency**: Ensure that the schema is consistent with the API's documentation and does not include any proprietary or branded terms.