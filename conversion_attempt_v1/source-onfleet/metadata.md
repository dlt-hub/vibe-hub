# Overview

This document provides a configuration guide for integrating with a third-party logistics API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various sync types and configurations.

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
            "name": "logistics_data",
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

- **Resource Name**: `logistics_data`
- **Endpoint Path and Method**: `/v1/resource` (default method: GET)
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout) with retries.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and downtime gracefully.

# Schema

- **Fields**: Use neutral field names such as `customer_id`, `order_id`, etc.
- **Types and Nullability**: Define field types and nullability based on the API's response schema, ensuring consistency and accuracy in data representation.

This configuration guide abstracts the integration process, providing a clean and vendor-neutral setup for accessing a third-party logistics API using dltHub's REST API source.