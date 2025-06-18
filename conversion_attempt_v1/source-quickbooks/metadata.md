# Overview

This document provides a configuration guide for integrating with a third-party accounting API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various resource endpoints and authentication methods.

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
            "name": "accounting_data",
            "endpoint": {
                "path": "/v1/accounting_data",
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

- **Resource Name**: `accounting_data`
- **Endpoint Path**: `/v1/accounting_data`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Fields**: The schema includes fields such as `id`, `amount`, `date`, etc., with types and nullability defined as per the API's response structure. Use neutral field names like `transaction_id` instead of branded terms.