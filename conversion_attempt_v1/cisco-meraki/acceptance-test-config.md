```markdown
# Overview

This document provides a configuration example for integrating with a third-party network management API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data retrieval and management.

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
            "name": "network_devices",
            "endpoint": {
                "path": "/v1/devices",
                "data_selector": "devices",
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

- **Type**: API Key
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

## Resources

- **Resource Name**: network_devices
- **Endpoint Path**: `/v1/devices`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `devices` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `device_id`, `device_name`, and `status`. All field names are generalized to ensure neutrality and avoid proprietary terms.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring accurate data representation.

This configuration provides a clean and vendor-neutral setup for integrating with a network management API, following dltHub's REST API source format and terminology.
```