```markdown
# Overview

This document provides a configuration guide for integrating with a third-party incident management API using dltHub's REST API source. The integration allows for data extraction from the API, supporting various resource configurations and authentication methods.

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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

## Resources

- **Resource Name**: `incident_data`
- **Endpoint Path**: `/v1/incidents`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **HTTP Status Codes**: Retry on status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts with a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `created_at`, `status`, and other relevant incident data.
- **Field Types**: Ensure all field types are consistent with the API's response structure.
- **Nullability**: Fields are nullable based on the API's data model.

This configuration provides a clean and generalized setup for integrating with a third-party incident management API using dltHub's REST API source, ensuring a vendor-neutral approach.
```