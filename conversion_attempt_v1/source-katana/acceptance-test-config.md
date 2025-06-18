# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data synchronization methods, including full refresh and incremental updates, depending on the API's capabilities.

## Configuration Example

Below is an example configuration for setting up a REST API source using dltHub. This configuration is designed to be flexible and adaptable to different API endpoints and authentication methods.

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

The API supports various authentication methods. In this example, an API key is used for authentication:

- **Type**: `api_key`
- **Key Name**: `api_key`
- **Location**: `query`

The API key should be securely stored and accessed using `dlt.secrets`.

## Resources

Each resource represents a specific endpoint of the API. The configuration includes:

- **Resource Name**: A neutral name for the resource.
- **Endpoint Path**: The path to the resource, e.g., `/v1/resource`.
- **HTTP Method**: Default is `GET`.
- **Pagination Strategy**: Uses an offset-based paginator with a limit of 100 records per request.
- **Data Selector**: Specifies the JSON path to the data array, e.g., `data`.

## Error Handling

The configuration includes error handling strategies to manage API response errors:

- **Retry Logic**: Automatically retries requests on specific HTTP status codes (e.g., 429, 500, 502, 503, 504).
- **Max Retries**: Up to 3 retries are allowed.
- **Backoff Factor**: A backoff factor of 2 is used to space out retries.

## Schema

The schema defines the structure of the data retrieved from the API. It includes:

- **Fields**: Neutral field names are used, such as `analytics_id` instead of any branded identifiers.
- **Types**: Data types are specified for each field.
- **Nullability**: Indicates whether a field can be null.

This guide ensures a clean and generalized approach to integrating with third-party APIs using dltHub, free from any proprietary or branded content.