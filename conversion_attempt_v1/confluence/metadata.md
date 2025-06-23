# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from the API's available resources, utilizing a vendor-neutral approach to ensure compatibility and ease of use.

## Configuration Example

Below is an example configuration for setting up a REST API source using dltHub. This configuration abstracts the parameters from the original YAML, ensuring a clean and generalized setup.

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

The integration uses API key authentication. The API key should be stored securely and referenced in the configuration as shown in the example. The key is included in the query parameters of each request.

- **Type**: api_key
- **Secrets**: `api_key` (stored in dlt.secrets)

## Resources

Each resource in the API is configured with the following parameters:

- **Resource Name**: A neutral name representing the resource.
- **Endpoint Path**: The path to the resource, e.g., `/v1/resource`.
- **HTTP Method**: Default is GET.
- **Pagination Strategy**: Uses offset pagination with a specified limit.
- **Data Selector**: Specifies the field path to extract data from the response.

## Error Handling

The configuration includes error handling strategies to manage common HTTP errors and ensure robust data synchronization:

- **Retry Logic**: Automatically retries requests on specific HTTP status codes.
- **Backoff Strategy**: Implements exponential backoff to handle rate limits and server errors.

Example error handling configuration:

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility and ease of integration. Fields are described with their types and nullability, abstracted from any proprietary naming conventions.

- **Field Names**: Use generic terms like `customer_id`, `event_id`, etc.
- **Types**: Specify data types such as string, integer, boolean.
- **Nullability**: Indicate if fields can be null.

This guide provides a clean and generalized approach to configuring a REST API source with dltHub, abstracting proprietary details and ensuring a vendor-neutral setup.