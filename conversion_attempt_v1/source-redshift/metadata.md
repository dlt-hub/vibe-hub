# Overview

This document provides a vendor-neutral configuration for integrating with a third-party database API using dltHub's REST API source format. The integration supports data synchronization from the database to your data pipeline.

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
            "name": "database_resource",
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

- **Resource Name**: `database_resource`
- **Endpoint Path and Method**: `/v1/resource`, default method is `GET`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Fallback/Handling Behavior**: Define fallback mechanisms for handling errors gracefully.

# Schema

- **Fields**: Use neutral field names such as `customer_id`, `transaction_id`, etc.
- **Types and Nullability**: Define field types and nullability based on the data structure provided by the API.

This configuration provides a clean and generalized setup for integrating with a third-party database API using dltHub's REST API source format, ensuring a seamless data synchronization process.