# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various data synchronization types, including incremental and full refresh, allowing for flexible data retrieval from the API.

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
            "name": "account_overview",
            "endpoint": {
                "path": "/v1/account_overview",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "agents_overview",
            "endpoint": {
                "path": "/v1/agents_overview",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "current_queue_activity",
            "endpoint": {
                "path": "/v1/current_queue_activity",
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
- **Secrets or Config Values**: The API key is stored securely using `dlt.secrets["api_key"]`.

# Resources

- **Resource Name**: `account_overview`, `agents_overview`, `current_queue_activity`
- **Endpoint Path and Method**: Each resource has a unique endpoint path, accessed via the default HTTP GET method.
- **Pagination Strategy**: Uses an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the response using the `data` field.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors, with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests) and 500-series server errors.

# Schema

- **Fields**: The schema includes fields such as `id`, `name`, and `current_timestamp`.
- **Types and Nullability**: Ensure fields are appropriately typed and handle nullability as per the API's response structure.
- **Neutral Field Names**: Use generic field names like `customer_id` instead of branded identifiers.