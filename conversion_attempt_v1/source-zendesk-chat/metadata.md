# Overview

This document provides a configuration guide for integrating with a third-party chat API using dltHub's REST API source. The integration allows for data extraction from the chat service, supporting various authentication methods and pagination strategies.

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
            "name": "chat_messages",
            "endpoint": {
                "path": "/v1/messages",
                "data_selector": "messages",
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

The integration supports OAuth2 authentication. The following secrets are required:

- `client_id`: The client ID for the OAuth2 application.
- `client_secret`: The client secret for the OAuth2 application.
- `token_url`: The URL to obtain the OAuth2 token.

# Resources

- **Resource Name**: chat_messages
- **Endpoint Path**: `/v1/messages`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: The data is located under the "messages" field in the response.

# Error Handling

The integration includes error handling with retry logic for specific HTTP status codes:

- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for the chat messages resource includes fields such as:

- `id`: Unique identifier for each message.
- `timestamp`: The time the message was sent.
- `content`: The content of the message.
- `sender_id`: Identifier for the message sender.

All field names are generalized to ensure vendor neutrality.