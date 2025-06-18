# Overview

This document provides a configuration guide for integrating with a third-party marketing automation API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data extraction and processing.

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
            "name": "marketing_data",
            "endpoint": {
                "path": "/v1/marketing_data",
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

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: Your client ID for the API.
  - `client_secret`: Your client secret for the API.
- **Token URL**: `https://api.example.com/oauth/token`

# Resources

- **Resource Name**: marketing_data
- **Endpoint Path**: `/v1/marketing_data`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Fields**: The schema should be defined based on the API's response structure, using neutral field names such as `customer_id`, `event_type`, etc.
- **Types and Nullability**: Ensure that the field types and nullability are consistent with the API's response.

This configuration provides a clean and vendor-neutral setup for integrating with a third-party marketing automation API using dltHub's REST API source. It abstracts away any proprietary or branded content, focusing solely on the reusable patterns and configurations necessary for the integration.