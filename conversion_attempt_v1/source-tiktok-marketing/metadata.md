# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization from various resources, allowing for efficient data extraction and management.

## Configuration Example

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

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: Abstracted to a neutral name such as `resource_name`.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: Default is `GET`.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

## Error Handling

- **Retry Logic**: Configured to retry on specific HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Implements a backoff factor of 2 with a maximum of 3 retries.
- **Timeouts and Limits**: Generalized messages for handling timeouts or data limits.

## Schema

- **Fields**: Use neutral field names such as `customer_id` or `user_key`.
- **Types and Nullability**: Ensure consistency with the API's response schema, using generic types and nullable fields where applicable.

This configuration guide abstracts the integration process, ensuring a vendor-neutral approach while maintaining the core functionality required for effective data synchronization.