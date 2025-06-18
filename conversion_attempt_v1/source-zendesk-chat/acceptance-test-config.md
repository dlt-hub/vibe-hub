# Overview

This document provides a vendor-neutral configuration for integrating with a third-party chat API using dltHub's REST API source. The integration supports both full refresh and incremental data synchronization, allowing for efficient data retrieval and management.

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

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: Your client ID for the API
  - `client_secret`: Your client secret for the API
- **Token URL**: `https://api.example.com/oauth/token`

# Resources

- **Resource Name**: chat_messages
- **Endpoint Path**: `/v1/messages`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `messages`

# Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Fields**: 
  - `id`: Unique identifier for each message
  - `timestamp`: The time the message was sent
  - `content`: The content of the message
  - `sender_id`: Identifier for the message sender

This configuration ensures a clean and efficient integration with the chat API, abstracting away any vendor-specific details and focusing on the essential parameters required for data synchronization.