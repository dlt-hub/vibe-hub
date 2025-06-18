# Overview

This document provides a configuration guide for integrating with a third-party database API using dltHub's REST API source format. The integration supports data synchronization from a generic database service, abstracting away any vendor-specific details.

## Configuration Example

Below is an example configuration for setting up a REST API source in dltHub. This configuration is designed to be vendor-neutral and can be adapted to various third-party database APIs.

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

The integration uses API key authentication. The API key should be stored securely and accessed via `dlt.secrets`.

- **Type**: `api_key`
- **Location**: `query`
- **Secret Key**: `api_key`

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: `GET` (default)
- **Pagination Strategy**: `offset`
  - **Limit**: `100`
- **Data Selector**: `data`

## Error Handling

The configuration includes basic error handling with retry logic for common HTTP status codes.

- **Retry on Status Codes**: `[429, 500, 502, 503, 504]`
- **Max Retries**: `3`
- **Backoff Factor**: `2`

## Schema

The schema for the data fields should be defined in a neutral manner, using generic field names. Ensure that the fields are consistent with the data structure provided by the API.

- **Primary Key**: `id`
- **Field Names**: Use neutral names like `customer_id`, `data`, etc.

This configuration provides a clean and generalized setup for integrating with a third-party database API using dltHub's REST API source. Adjust the parameters as needed to fit the specific API you are working with.