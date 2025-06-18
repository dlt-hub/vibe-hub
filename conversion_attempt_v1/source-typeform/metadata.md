```markdown
# Overview

This document provides a configuration guide for integrating with a third-party form-based API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

## Configuration Example

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
            "name": "form_responses",
            "endpoint": {
                "path": "/v1/forms/{form_id}/responses",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: Your client ID for the API
  - `client_secret`: Your client secret for the API
  - `token_url`: The URL to obtain the OAuth2 token

## Resources

- **Resource Name**: form_responses
- **Endpoint Path**: `/v1/forms/{form_id}/responses`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request
- **Data Selector**: `items`

## Error Handling

- **Retry/Backoff Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - `id`: Unique identifier for each response
  - `form_id`: Identifier for the form
  - `response_data`: Data collected from the form response
- **Types**: 
  - `id`: String
  - `form_id`: String
  - `response_data`: JSON
- **Nullability**: 
  - `id`: Not nullable
  - `form_id`: Not nullable
  - `response_data`: Nullable

This configuration allows for seamless integration with the API, providing a robust solution for data extraction and synchronization.
```
