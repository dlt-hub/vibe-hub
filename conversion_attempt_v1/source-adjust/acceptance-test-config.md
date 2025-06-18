```markdown
# Overview

This document provides a guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for both full and incremental data refreshes.

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

The API requires authentication via an API key. The key should be included in the query parameters of each request.

- **Type**: `api_key`
- **Configuration**: 
  - `name`: "api_key"
  - `api_key`: Retrieved from `dlt.secrets["api_key"]`
  - `location`: "query"

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is located under the "data" field in the response.

## Error Handling

The integration includes error handling mechanisms to manage common HTTP errors and ensure reliable data retrieval.

- **Retry Logic**: 
  - Retries on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

The schema for the data retrieved from the API is defined by the fields present in the response. Field names are generalized to ensure neutrality and avoid any proprietary references.

- **Fields**: 
  - `id`: Primary key
  - `data`: Contains the main data payload

This configuration ensures a clean and vendor-neutral integration with the third-party analytics API, following dltHub's REST API source format.
```