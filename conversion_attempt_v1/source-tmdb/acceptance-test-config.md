# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports full refresh sync types, allowing you to retrieve data from the API and store it in a structured format.

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
            "name": "trending",
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

- **Resource Name**: `trending`
- **Endpoint Path and Method**: `/v1/resource`, default method is GET.
- **Pagination Strategy**: Offset pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout) with retries.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and downtime gracefully.

# Schema

- **Fields**: The schema should be defined based on the API response structure, using neutral field names such as `analytics_id` instead of any branded identifiers.
- **Types and Nullability**: Ensure that the schema accurately reflects the data types and allows for null values where applicable.

This configuration guide provides a clean, vendor-neutral approach to integrating with a third-party analytics API using dltHub's REST API source. Follow the outlined steps to set up and manage your data integration effectively.