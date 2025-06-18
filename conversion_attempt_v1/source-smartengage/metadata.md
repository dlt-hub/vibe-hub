# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization through a RESTful interface, allowing for efficient data extraction and transformation.

# Configuration Example

Below is an example configuration for setting up a REST API source using dltHub. This configuration is designed to be vendor-neutral and can be adapted to various third-party APIs by adjusting the base URL, authentication details, and resource-specific settings.

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

The integration uses API key-based authentication. The API key should be stored securely and referenced in the configuration as shown in the example. The key is included in the query parameters of each request.

- **Type**: `api_key`
- **Location**: `query`
- **Secret Key**: `api_key`

# Resources

Each resource represents a specific data endpoint within the API. The configuration includes details such as the endpoint path, pagination strategy, and data selection method.

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: `GET` (default)
- **Pagination Strategy**: `offset`
  - **Limit**: `100`
- **Data Selector**: `data`

# Error Handling

The configuration includes basic error handling strategies to manage common HTTP errors and ensure reliable data extraction.

- **Retry on Status Codes**: `[429, 500, 502, 503, 504]`
- **Max Retries**: `3`
- **Backoff Factor**: `2`

# Schema

The schema defines the structure of the data extracted from the API. Field names are generalized to ensure compatibility with various data sources.

- **Primary Key**: `id`
- **Field Names**: Use neutral terms such as `customer_id`, `event_type`, etc.

This configuration provides a flexible and reusable template for integrating with RESTful APIs using dltHub, ensuring a clean and efficient data synchronization process.