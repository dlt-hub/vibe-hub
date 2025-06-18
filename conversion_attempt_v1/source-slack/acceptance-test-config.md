# Overview

This document provides a configuration guide for integrating with a third-party messaging API using dltHub's REST API source. The integration supports both full refresh and incremental sync types, allowing for efficient data retrieval and updates.

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
        "primary_key": "message_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "messages",
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

## Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: Your OAuth client ID
  - `client_secret`: Your OAuth client secret
- **Token URL**: `https://api.example.com/oauth/token`

## Resources

- **Resource Name**: messages
- **Endpoint Path**: `/v1/messages`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `messages`

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**:
  - `message_id`: Unique identifier for each message
  - `timestamp`: The time the message was sent
  - `content`: The content of the message
  - `user_id`: Identifier for the user who sent the message

This configuration ensures a robust and efficient integration with the third-party messaging API, leveraging dltHub's capabilities for data extraction and synchronization.