# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data synchronization types, including full refresh and incremental updates, with a focus on handling large datasets efficiently through pagination and error handling strategies.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/oauth/token"
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
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: Your client ID for the API
  - `client_secret`: Your client secret for the API
- **Token URL**: `https://api.example.com/oauth/token`

# Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Fields**: 
  - Use neutral field names such as `customer_id`, `event_time`, etc.
- **Types**: Ensure consistency with the API's data types
- **Nullability**: Define fields as nullable based on the API's response structure

This configuration provides a clean and generalized setup for integrating with a third-party analytics API using dltHub's REST API source. It abstracts away any vendor-specific details, ensuring a vendor-neutral approach to API integration.