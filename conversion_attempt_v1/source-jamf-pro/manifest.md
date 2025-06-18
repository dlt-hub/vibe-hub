# Overview

This document provides a configuration example for integrating with a mobile device management API using dltHub's REST API source format. The integration allows for the retrieval of computer inventory data from a third-party service, utilizing a session token-based authentication mechanism and a page number pagination strategy.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["username"],
            "client_secret": dlt.secrets["password"],
            "token_url": "https://api.example.com/api/v1/auth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "computers",
            "endpoint": {
                "path": "/api/v1/computers-inventory",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100,
                    "start_from_page": 0
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: Retrieved from `dlt.secrets["username"]`
- **Client Secret**: Retrieved from `dlt.secrets["password"]`
- **Token URL**: `https://api.example.com/api/v1/auth/token`

# Resources

- **Resource Name**: computers
- **Endpoint Path**: `/api/v1/computers-inventory`
- **HTTP Method**: GET
- **Pagination Strategy**: Page number
  - **Page Parameter**: `page`
  - **Limit**: 100
  - **Start from Page**: 0
- **Data Selector**: `results`

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes with exponential backoff.
- **Known HTTP Response Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for the `computers` resource includes the following fields:

- **id**: string (required)
- **name**: string or null
- **lastContactTime**: string or null
- **capableUsers**: array of strings or null
- **managementId**: string or null

Additional fields are included as per the API's response structure, with types and nullability specified. All field names are generalized to ensure vendor neutrality.