# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and configuration settings. The API uses a session token-based authentication mechanism and supports pagination and error handling strategies.

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
            "token_url": "https://login.example.com/services/oauth2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/v5/objects/campaigns",
                "data_selector": "values",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageUrl",
                    "stop_condition": "not response.get('nextPageUrl', {})"
                }
            }
        },
        {
            "name": "email_clicks",
            "endpoint": {
                "path": "/emailClick/version/4/do/query",
                "data_selector": "result.emailClick",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "id_greater_than",
                    "page_size": 200,
                    "stop_condition": "not response.result.emailClick or len(response.result.emailClick) < 200"
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

The API uses OAuth2 authentication. The following secrets are required:

- `client_id`: The client ID for the OAuth2 application.
- `client_secret`: The client secret for the OAuth2 application.
- `refresh_token`: The refresh token for obtaining new access tokens.

The token URL is `https://login.example.com/services/oauth2/token`.

# Resources

## Campaigns

- **Endpoint Path**: `/v5/objects/campaigns`
- **Data Selector**: `values`
- **Pagination Strategy**: Cursor-based using `nextPageUrl`

## Email Clicks

- **Endpoint Path**: `/emailClick/version/4/do/query`
- **Data Selector**: `result.emailClick`
- **Pagination Strategy**: Cursor-based using `id_greater_than` with a page size of 200

# Error Handling

The API implements a composite error handler with the following strategies:

- **Retry on Status Codes**: 429 (Rate Limited)
- **Max Retries**: 12 for rate-limited errors, 10 for other errors
- **Backoff Strategies**:
  - Constant backoff of 7200 seconds for rate-limited errors
  - Exponential backoff with a factor of 3 for other errors

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to ensure vendor neutrality. For example, `id`, `name`, `createdAt`, and `updatedAt` are common fields across resources.