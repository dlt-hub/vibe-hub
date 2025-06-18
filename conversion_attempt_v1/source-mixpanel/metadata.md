# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data streams and allows for efficient data synchronization through a RESTful interface.

## Configuration Example

Below is an example configuration for setting up the REST API source using dltHub. This configuration includes client authentication, resource definitions, and pagination strategies.

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

The API requires authentication using an API key. The key should be stored securely and referenced in the configuration as shown above. The API key is included in the query parameters of each request.

- **Type**: `api_key`
- **Location**: `query`
- **Key Name**: `api_key`

## Resources

The integration supports multiple resources, each corresponding to a different data stream. Each resource is defined with a unique endpoint path and may include specific pagination strategies.

- **Resource Name**: Abstracted to `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: `GET` (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

## Error Handling

The integration includes error handling mechanisms to manage common HTTP errors and ensure reliable data synchronization.

- **Retry Logic**: The system retries requests on specific HTTP status codes with exponential backoff.
- **Retry Status Codes**: `[429, 500, 502, 503, 504]`
- **Max Retries**: `3`
- **Backoff Factor**: `2`

## Schema

The schema for each resource is defined with neutral field names to ensure compatibility and avoid vendor-specific terminology. Fields are abstracted to generic names such as `analytics_id` and `customer_id`.

- **Field Names**: Use neutral terms like `analytics_id`, `customer_id`
- **Data Types**: Consistent with the API's response structure
- **Nullability**: Defined based on the API's data model

This configuration guide provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format. Ensure all parameters are sourced from the provided YAML and generalized to remove any proprietary content.