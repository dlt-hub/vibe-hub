# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization from various resources available through the API, utilizing a secure and efficient connection setup.

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
            "name": "blocks",
            "endpoint": {
                "path": "/v1/blocks",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "databases",
            "endpoint": {
                "path": "/v1/databases",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "pages",
            "endpoint": {
                "path": "/v1/pages",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
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

The integration uses OAuth2 authentication to securely access the API. The following credentials are required:

- `client_id`: Your application's client ID.
- `client_secret`: Your application's client secret.
- `token_url`: The URL to obtain the OAuth2 token.

These credentials should be stored securely and accessed via `dlt.secrets`.

# Resources

The API provides access to several resources, each with its own endpoint and pagination strategy:

- **Blocks**
  - Endpoint: `/v1/blocks`
  - Pagination: Offset-based with a limit of 100 items per request.

- **Databases**
  - Endpoint: `/v1/databases`
  - Pagination: Offset-based with a limit of 100 items per request.

- **Pages**
  - Endpoint: `/v1/pages`
  - Pagination: Offset-based with a limit of 100 items per request.

- **Users**
  - Endpoint: `/v1/users`
  - Pagination: Offset-based with a limit of 100 items per request.

# Error Handling

The integration includes robust error handling mechanisms to ensure reliable data synchronization:

- **Retry Logic**: Automatically retries requests on specific HTTP status codes (e.g., 429, 500, 502, 503, 504) with a maximum of 3 retries and an exponential backoff factor of 2.
- **Timeouts**: Configurable timeouts to handle slow responses or network issues.

# Schema

The schema for each resource is defined with neutral field names to ensure compatibility and ease of use. Each resource includes a primary key for data integrity and supports a merge write disposition to handle updates efficiently.