# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data synchronization types, including full refresh and incremental updates, with a focus on handling large datasets through pagination and error management.

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
- **Secrets or Config Values**: 
  - `api_key`: Stored securely in dlt's secrets management system.
  - **Location**: The API key is included in the query parameters of each request.

# Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: 
  - **Path**: `/v1/resource`
  - **Method**: GET (default)
- **Pagination Strategy**: 
  - **Type**: `offset`
  - **Limit**: 100 records per request
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: 
  - The integration includes retry logic for handling transient errors, with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: 
  - The system is configured to retry on HTTP status codes 429, 500, 502, 503, and 504.
- **Fallback/Handling Behavior**: 
  - In case of persistent errors, the integration will log the issue and halt further requests to prevent unnecessary load on the API.

# Schema

- **Fields**: The schema is defined based on the expected data structure from the API, using neutral field names such as `customer_id` and `transaction_amount`.
- **Types and Nullability**: Each field's data type and nullability are configured according to the API's documentation, ensuring accurate data representation and integrity.

This configuration provides a robust framework for integrating with a third-party analytics API, leveraging dltHub's capabilities for secure authentication, efficient data retrieval, and reliable error handling.