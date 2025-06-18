# Overview

This document provides a configuration guide for integrating with a third-party shipping API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data retrieval and management.

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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests) and 5xx server errors with retries.
- **Fallback Behavior**: Define fallback mechanisms for handling unexpected errors or timeouts.

## Schema

- **Fields**: The schema should be defined with neutral field names, ensuring compatibility with the data structure provided by the API.
- **Types and Nullability**: Ensure that field types and nullability are consistent with the API's data model.

This configuration guide abstracts the integration process, providing a clean and vendor-neutral approach to setting up a REST API source with dltHub.