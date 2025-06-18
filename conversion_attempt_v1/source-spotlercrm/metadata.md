# Overview

This document provides a configuration guide for integrating with a third-party CRM API using dltHub's REST API source. The integration allows for data extraction from the CRM system, supporting various data synchronization types.

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
            "name": "crm_data",
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

- **Resource Name**: `crm_data`
- **Endpoint Path and Method**: `/v1/resource`, using the default HTTP GET method.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Fallback/Handling Behavior**: Configure retries with a maximum of 3 attempts and a backoff factor of 2.

# Schema

- **Fields**: Use neutral field names such as `customer_id`, `transaction_date`, and `amount`.
- **Types and Nullability**: Define field types and specify if they can be null, ensuring consistency with the API's data structure.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party CRM API using dltHub's REST API source, ensuring a seamless data extraction process.