# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports both full refresh and incremental sync types, allowing for flexible data retrieval based on your needs.

## Configuration Example

Below is a sample configuration for setting up a REST API source using dltHub. This example demonstrates how to configure client authentication, resource endpoints, and pagination.

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

The API requires API key-based authentication. The API key should be stored securely and accessed via `dlt.secrets`.

- **Type**: `api_key`
- **Location**: `query`
- **Secret Key**: `api_key`

## Resources

Each resource represents a distinct data endpoint within the API. The configuration includes:

- **Resource Name**: A neutral identifier for the resource.
- **Endpoint Path**: The API path to access the resource, e.g., `/v1/resource`.
- **HTTP Method**: Default is `GET`.
- **Pagination Strategy**: Uses an offset-based paginator with a specified limit.
- **Data Selector**: The JSON path to extract data from the API response.

## Error Handling

The integration includes robust error handling to manage common HTTP errors and ensure reliable data retrieval.

- **Retry Logic**: Automatically retries on specific HTTP status codes.
- **Backoff Strategy**: Implements exponential backoff to handle rate limits and server errors.
- **Known HTTP Response Codes**: Handles 429, 500, 502, 503, and 504 status codes.

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

The schema defines the structure of the data retrieved from the API. It includes field names, types, and nullability, using neutral identifiers.

- **Fields**: Use generic names like `customer_id`, `event_timestamp`, etc.
- **Types**: Specify data types such as `string`, `integer`, `boolean`.
- **Nullability**: Indicate whether fields can be null.

This guide ensures a clean, vendor-neutral integration with a third-party analytics API using dltHub's REST API source format, abstracting away any proprietary or branded content.