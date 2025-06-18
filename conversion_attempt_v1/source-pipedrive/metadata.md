# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization through a RESTful API, allowing for efficient data extraction and management.

# Configuration Example

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

# Authentication

The integration uses API key authentication. The API key should be stored securely and referenced in the configuration as shown in the example. The key is included in the query parameters of each request.

- **Type**: `api_key`
- **Location**: `query`
- **Key Name**: `api_key`

# Resources

Each resource represents a data endpoint in the API. The configuration includes:

- **Resource Name**: A neutral name for the data resource.
- **Endpoint Path**: The path to the resource, e.g., `/v1/resource`.
- **HTTP Method**: Default is `GET`.
- **Pagination Strategy**: Uses offset pagination with a specified limit.
- **Data Selector**: The field path to extract data from the response.

# Error Handling

The integration includes error handling mechanisms to manage common HTTP errors and retry logic:

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema defines the structure of the data fields, using neutral field names. Each field includes its type and nullability status. For example, use `customer_id` instead of any branded identifiers.

This configuration guide ensures a clean and vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source.