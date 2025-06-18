# Overview

This document provides a vendor-neutral configuration for integrating with a third-party support API using dltHub's REST API source format. The integration supports both full refresh and incremental data synchronization, allowing for efficient data retrieval and management.

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
            "name": "support_tickets",
            "endpoint": {
                "path": "/v1/tickets",
                "data_selector": "tickets",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "ticket_comments",
            "endpoint": {
                "path": "/v1/ticket_comments",
                "data_selector": "comments",
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

The integration uses OAuth2 authentication. The following secrets are required:

- `client_id`: The client ID for the OAuth2 application.
- `client_secret`: The client secret for the OAuth2 application.
- `token_url`: The URL to obtain the OAuth2 token.

## Resources

### Support Tickets

- **Resource Name**: support_tickets
- **Endpoint Path**: `/v1/tickets`
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: `tickets`

### Ticket Comments

- **Resource Name**: ticket_comments
- **Endpoint Path**: `/v1/ticket_comments`
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: `comments`

## Error Handling

The integration includes error handling with retry logic for common HTTP status codes. The configuration is as follows:

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

## Schema

The schema for each resource includes fields with neutral names and types. The primary key for each resource is `id`, and the data is merged based on this key. The schema is designed to be flexible and accommodate changes in the API structure.