# Overview

This document provides a vendor-neutral configuration for integrating with a third-party database API using dltHub's REST API source format. The integration supports data synchronization from the database to your data pipeline.

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

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use an exponential backoff factor of 2 with a maximum of 3 retries.

## Schema

- **Fields**: Use neutral field names such as `customer_id`, `transaction_id`, etc.
- **Types and Nullability**: Define field types and nullability based on the database schema.

This configuration provides a clean and generalized setup for integrating with a third-party database API using dltHub's REST API source format. It abstracts away any proprietary or branded content, ensuring a vendor-neutral approach.