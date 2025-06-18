# Overview

This document provides a configuration guide for integrating with a third-party email marketing API using dltHub's REST API source. The integration supports data synchronization from various resources provided by the API, such as email activity, campaigns, lists, and reports.

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
            "token_url": "https://login.example.com/oauth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "email_activity",
            "endpoint": {
                "path": "/v1/email_activity",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "lists",
            "endpoint": {
                "path": "/v1/lists",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "reports",
            "endpoint": {
                "path": "/v1/reports",
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

The integration uses OAuth2 authentication. The following secrets are required:

- `client_id`: The client ID for the OAuth2 application.
- `client_secret`: The client secret for the OAuth2 application.
- `token_url`: The URL to obtain the OAuth2 token.

# Resources

- **Resource Name**: email_activity
  - **Endpoint Path**: `/v1/email_activity`
  - **Pagination Strategy**: Offset pagination with a limit of 100
  - **Data Selector**: `data`

- **Resource Name**: campaigns
  - **Endpoint Path**: `/v1/campaigns`
  - **Pagination Strategy**: Offset pagination with a limit of 100
  - **Data Selector**: `data`

- **Resource Name**: lists
  - **Endpoint Path**: `/v1/lists`
  - **Pagination Strategy**: Offset pagination with a limit of 100
  - **Data Selector**: `data`

- **Resource Name**: reports
  - **Endpoint Path**: `/v1/reports`
  - **Pagination Strategy**: Offset pagination with a limit of 100
  - **Data Selector**: `data`

# Error Handling

The integration includes error handling with retry logic for common HTTP status codes:

- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource includes fields that are neutral and generalized. For example, fields like `customer_id` or `transaction_id` are used instead of proprietary or branded terms. Each field's type and nullability are consistent with the API's response structure.