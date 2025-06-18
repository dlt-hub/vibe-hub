# Overview

This document provides a configuration guide for integrating with a third-party incident management API using dltHub's REST API source. The integration supports both full refresh and incremental data synchronization, allowing users to efficiently manage and retrieve incident and service data.

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
            "name": "incidents",
            "endpoint": {
                "path": "/v1/incidents",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "services",
            "endpoint": {
                "path": "/v1/services",
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

### Incidents

- **Endpoint Path**: `/v1/incidents`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

### Services

- **Endpoint Path**: `/v1/services`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `status`, and other relevant attributes for incidents and services.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, using neutral field names to ensure compatibility and generalization.

This configuration guide ensures a clean and vendor-neutral setup for integrating with a third-party incident management API using dltHub's REST API source.