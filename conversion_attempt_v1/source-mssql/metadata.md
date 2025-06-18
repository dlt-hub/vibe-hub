# Overview

This document provides a configuration guide for integrating with a third-party database API using dltHub's REST API source format. The integration supports data synchronization from a database source, abstracting vendor-specific details to ensure a clean and reusable setup.

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

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely manage your API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `database_resource`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

## Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema is defined with neutral field names, such as `id`, `name`, and `value`.
- **Types**: Ensure that field types are consistent with the data returned by the API.
- **Nullability**: Fields are nullable unless specified otherwise.

This configuration provides a vendor-neutral setup for integrating with a third-party database API, ensuring compatibility with dltHub's REST API source format.