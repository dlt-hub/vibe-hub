# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various synchronization types, including full refresh and incremental updates, allowing for flexible data retrieval from the API.

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
            "name": "cash_drawers",
            "endpoint": {
                "path": "/v1/cash_drawers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "bank_accounts",
            "endpoint": {
                "path": "/v1/bank_accounts",
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
  - `client_id`: Your client ID for the API
  - `client_secret`: Your client secret for the API
  - `token_url`: The URL to obtain the OAuth2 token

# Resources

- **Resource Name**: cash_drawers
  - **Endpoint Path**: `/v1/cash_drawers`
  - **Pagination Strategy**: Offset with a limit of 100
  - **Data Selector**: `data`

- **Resource Name**: bank_accounts
  - **Endpoint Path**: `/v1/bank_accounts`
  - **Pagination Strategy**: Offset with a limit of 100
  - **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle HTTP status codes such as 429 (Too Many Requests) and 500 (Internal Server Error) with retries.
- **Fallback/Handling Behavior**: Log errors and continue processing other resources if a specific resource fails.

# Schema

- **Fields**: Use neutral field names such as `id`, `name`, `created_at`, etc.
- **Types**: Ensure data types are consistent with the API's response.
- **Nullability**: Handle nullable fields appropriately based on the API's schema.

This configuration provides a clean and generalized setup for integrating with a third-party analytics API using dltHub's REST API source, ensuring a vendor-neutral approach.