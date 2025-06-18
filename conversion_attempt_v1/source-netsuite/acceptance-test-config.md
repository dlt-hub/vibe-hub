# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data synchronization types, including full refresh and incremental updates, allowing for efficient data extraction and management.

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
- **Secrets or Config Values**: Use neutral keys such as `api_key` stored securely in `dlt.secrets`.

# Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource`, default method is GET
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Fallback/Handling Behavior**: Define fallback mechanisms for handling errors and ensuring data consistency.

# Schema

- **Fields, Types, Nullability**: Define schema fields using neutral names such as `customer_id`, `transaction_id`, etc., ensuring consistency with the data structure provided by the API.
- **Use Neutral Field Names**: Avoid using branded or proprietary field names, opting for generic alternatives.

This configuration ensures a clean and efficient integration with a third-party analytics API, adhering to dltHub's REST API source format and best practices.