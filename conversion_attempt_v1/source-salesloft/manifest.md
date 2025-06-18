# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API uses either API Key or OAuth2 for authentication.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",  # or "oauth2"
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "query"  # or "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "response['metadata']['paging']['next_page']",
                    "stop_condition": "not response['metadata']['paging']['next_page']"
                }
            }
        },
        {
            "name": "account_tiers",
            "endpoint": {
                "path": "/account_tiers",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "response['metadata']['paging']['next_page']",
                    "stop_condition": "not response['metadata']['paging']['next_page']"
                }
            }
        },
        // Additional resources follow the same pattern
    ]
})
```

## Authentication

- **Type**: `api_key` or `oauth2`
- **Secrets or Config Values**:
  - For API Key: `api_key`
  - For OAuth2: `client_id`, `client_secret`, `refresh_token`

## Resources

Each resource is defined with the following parameters:

- **Resource Name**: Abstracted from the original name
- **Endpoint Path**: The path to the resource, e.g., `/users`
- **HTTP Method**: Default is `GET`
- **Pagination Strategy**: Uses cursor-based pagination with a limit of 100
- **Data Selector**: Extracts data from the `data` field

## Error Handling

- **Retry/Backoff Logic**: Not explicitly defined in the YAML, but can be configured as needed
- **Known HTTP Response Codes**: Handle standard HTTP errors like 429, 500, etc.
- **Fallback/Handling Behavior**: Implement custom logic as required

## Schema

The schema for each resource is defined with neutral field names and types. For example:

```yaml
users:
  type: object
  properties:
    id:
      type: integer
    name:
      type: string
    email:
      type: string
    created_at:
      type: string
      format: date-time
    updated_at:
      type: string
      format: date-time
```

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source.