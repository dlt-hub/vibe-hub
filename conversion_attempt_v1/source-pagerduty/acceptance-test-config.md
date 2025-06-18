# Overview

This document provides a configuration guide for integrating with a third-party incident management API using dltHub's REST API source. The integration supports full data synchronization from the API, with a focus on retrieving incident data and related resources.

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
            "name": "incident_data",
            "endpoint": {
                "path": "/v1/incidents",
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
- **Configuration**: The API key should be stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

## Resources

- **Resource Name**: `incident_data`
- **Endpoint Path**: `/v1/incidents`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `status`, `created_at`, and other relevant incident attributes.
- **Types**: Ensure that field types are consistent with the API's response format.
- **Nullability**: Fields are nullable based on the API's data model.

This configuration provides a clean and vendor-neutral setup for integrating with a third-party incident management API using dltHub's REST API source. It abstracts away any proprietary or branded content, focusing solely on the reusable patterns and parameters necessary for the integration.