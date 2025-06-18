# Overview

This document provides a configuration example for integrating with a third-party video conferencing API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

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
            "name": "meeting_registration_questions",
            "endpoint": {
                "path": "/v1/meeting_registration_questions",
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

## Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: The client ID for the OAuth2 application.
  - `client_secret`: The client secret for the OAuth2 application.
- **Token URL**: `https://api.example.com/oauth/token`

## Resources

- **Resource Name**: meeting_registration_questions
- **Endpoint Path**: `/v1/meeting_registration_questions`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - `id`: Primary key for the resource.
  - Other fields are abstracted and should be defined based on the API's response structure.

This configuration provides a clean and generalized setup for integrating with a third-party video conferencing API using dltHub's REST API source, ensuring a vendor-neutral approach.