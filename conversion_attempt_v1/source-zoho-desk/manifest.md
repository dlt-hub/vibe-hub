# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with specific endpoints and configurations. The API uses OAuth2 for authentication and supports pagination for data retrieval.

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
            "refresh_token": dlt.secrets["refresh_token"],
            "token_url": dlt.secrets["token_refresh_endpoint"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "organizations",
            "endpoint": {
                "path": "/organizations",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "agents",
            "endpoint": {
                "path": "/agents",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "tickets",
            "endpoint": {
                "path": "/tickets",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        }
        // Additional resources can be added here following the same structure
    ]
})
```

# Authentication

The API uses OAuth2 authentication. The following secrets are required for authentication:

- `client_id`: The client ID for the OAuth2 application.
- `client_secret`: The client secret for the OAuth2 application.
- `refresh_token`: The refresh token for obtaining new access tokens.
- `token_refresh_endpoint`: The endpoint for refreshing the access token.

# Resources

Each resource is defined with a name, endpoint path, and pagination strategy. The data selector specifies the field path to extract data from the API response.

- **Organizations**
  - Path: `/organizations`
  - Data Selector: `data`
  - Pagination: Offset with a limit of 50

- **Agents**
  - Path: `/agents`
  - Data Selector: `data`
  - Pagination: Offset with a limit of 50

- **Tickets**
  - Path: `/tickets`
  - Data Selector: `data`
  - Pagination: Offset with a limit of 50

# Error Handling

The configuration includes retry logic for handling errors. The API will retry requests on specific HTTP status codes with exponential backoff.

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to ensure vendor neutrality.

- **Organizations Schema**
  - Fields: `id`, `companyName`, `country`, `currencyCode`, etc.
  - Types: String, Number, Boolean
  - Nullability: Fields can be null

- **Agents Schema**
  - Fields: `id`, `firstName`, `lastName`, `emailId`, etc.
  - Types: String, Boolean
  - Nullability: Fields can be null

- **Tickets Schema**
  - Fields: `id`, `subject`, `status`, `createdTime`, etc.
  - Types: String, Boolean
  - Nullability: Fields can be null

This configuration provides a comprehensive setup for integrating with a third-party analytics API using dltHub's REST API source. The setup includes authentication, resource configuration, error handling, and schema definitions.