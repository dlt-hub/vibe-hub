# Overview

This document provides a configuration example for integrating with a third-party communication-testing platform's REST API. The integration allows fetching data from various resources such as messages, servers, and usage transactions. The API supports basic authentication and provides data in a structured format.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "messages",
            "endpoint": {
                "path": "/v1/messages",
                "data_selector": "items"
            }
        },
        {
            "name": "servers",
            "endpoint": {
                "path": "/v1/servers",
                "data_selector": "items"
            }
        },
        {
            "name": "usage_transactions",
            "endpoint": {
                "path": "/v1/usage/transactions",
                "data_selector": "items"
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**:
  - `username`: The username for API access, typically "api".
  - `password`: The API key used as a password.

# Resources

## Messages
- **Endpoint Path**: `/v1/messages`
- **Primary Key**: `id`
- **Data Selector**: `items`

## Servers
- **Endpoint Path**: `/v1/servers`
- **Primary Key**: `id`
- **Data Selector**: `items`

## Usage Transactions
- **Endpoint Path**: `/v1/usage/transactions`
- **Primary Key**: `timestamp`
- **Data Selector**: `items`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use an exponential backoff strategy with a maximum of 3 retries and a backoff factor of 2.

# Schema

## Messages
- **Fields**:
  - `id`: string
  - `type`: string or null
  - `attachments`: number or null
  - `bcc`: array or null
  - `cc`: array or null
  - `from`: array or null
  - `read`: boolean or null
  - `received`: string or null
  - `server`: string or null
  - `subject`: string or null
  - `summary`: string or null
  - `to`: array or null

## Servers
- **Fields**:
  - `id`: string
  - `customId`: string or null
  - `domain`: string or null
  - `messages`: number or null
  - `name`: string or null
  - `restricted`: boolean or null
  - `retention`: number or null
  - `users`: array or null

## Usage Transactions
- **Fields**:
  - `timestamp`: string
  - `email`: number or null
  - `emailSent`: number or null
  - `previews`: number or null
  - `screenshots`: number or null
  - `sms`: number or null
  - `smsSent`: number or null

This configuration provides a clean and vendor-neutral setup for integrating with a third-party communication-testing platform's REST API using dltHub's REST API source format.