# Overview

This document provides a configuration guide for integrating with a third-party messaging API using dltHub's REST API source. The integration supports various data resources such as users, channels, and messages, allowing for efficient data synchronization and retrieval.

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
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "channels",
            "endpoint": {
                "path": "/v1/channels",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "messages",
            "endpoint": {
                "path": "/v1/messages",
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

The integration uses OAuth2 authentication. Ensure that the following secrets are configured in your dltHub environment:

- `client_id`: Your OAuth2 client ID.
- `client_secret`: Your OAuth2 client secret.

The token URL for obtaining access tokens is `https://api.example.com/oauth/token`.

## Resources

### Users

- **Endpoint Path**: `/v1/users`
- **Pagination**: Offset-based with a limit of 100 records per request.
- **Data Selector**: `data`

### Channels

- **Endpoint Path**: `/v1/channels`
- **Pagination**: Offset-based with a limit of 100 records per request.
- **Data Selector**: `data`

### Messages

- **Endpoint Path**: `/v1/messages`
- **Pagination**: Offset-based with a limit of 100 records per request.
- **Data Selector**: `data`

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

The schema for each resource includes fields such as `id`, `name`, and `created_at`. All field names are generalized to ensure vendor neutrality. Ensure that your data processing logic accommodates these fields appropriately.