# Overview

This document provides a configuration example for integrating with a generic database API using dltHub's REST API source format. The integration supports data synchronization from a third-party database service, abstracting away any vendor-specific details.

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

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: Default is `GET`
- **Pagination Strategy**: Uses an `offset` paginator with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

## Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields with neutral names such as `id`, `name`, and `value`.
- **Types**: Ensure that field types are consistent with the data returned by the API.
- **Nullability**: Fields may be nullable depending on the API's response structure.

This configuration provides a clean, vendor-neutral setup for integrating with a generic database API using dltHub's REST API source format. It abstracts away any proprietary details, focusing on reusable patterns and configurations.