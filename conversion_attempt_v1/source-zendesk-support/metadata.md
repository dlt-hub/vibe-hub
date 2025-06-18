# Overview

This document provides a configuration guide for integrating with a third-party customer support API using dltHub's REST API source. The integration allows for data synchronization from various resources such as brands, groups, organizations, and more. The API supports OAuth2 authentication and offers pagination capabilities for efficient data retrieval.

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
                    "type": "cursor",
                    "cursor_path": "next_cursor",
                    "cursor_param": "cursor"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: Your client ID for the API.
  - `client_secret`: Your client secret for the API.
- **Token URL**: `https://api.example.com/oauth/token`

# Resources

- **Resource Name**: Abstracted from the specific API resource.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Cursor-based pagination using `cursor` as the parameter and `next_cursor` as the path for the next page.
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: 
  - Retry on status codes: [429, 500, 502, 503, 504]
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Fields**: Use neutral field names such as `customer_id`, `resource_id`, etc.
- **Types**: Consistent with the API's data types.
- **Nullability**: Defined based on the API's response schema.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party customer support API using dltHub's REST API source. It abstracts away any proprietary or branded content, ensuring a generic and reusable integration pattern.