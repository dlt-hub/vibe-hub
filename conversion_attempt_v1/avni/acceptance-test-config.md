# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data synchronization types, including full refresh and incremental updates, ensuring efficient data retrieval and management.

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

The integration uses API key-based authentication. The API key is securely stored and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request to authenticate API calls.

# Resources

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

# Error Handling

The integration includes robust error handling mechanisms:
- **Retry Logic**: Automatically retries requests on encountering HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Implements an exponential backoff with a factor of 2, allowing up to 3 retries for transient errors.

# Schema

The schema is designed to be flexible and neutral, using generic field names such as `customer_id` and `event_timestamp`. Each field's type and nullability are defined based on the API's response structure, ensuring compatibility and data integrity.

This guide ensures a clean and efficient integration with a third-party analytics API, leveraging dltHub's REST API source capabilities while maintaining a vendor-neutral approach.