# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and configuration settings. The API allows for data retrieval with pagination and error handling capabilities.

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
            "token_url": "https://id.example.com/api/v2/oauth2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge",
        "endpoint": {
            "params": {
                "per_page": 50
            },
            "headers": {
                "Account-Id": dlt.secrets["account_id"]
            },
            "error_handler": {
                "retry_on_status_codes": [401, 403, 404],
                "max_retries": 3,
                "backoff_factor": 2
            }
        }
    },
    "resources": [
        {
            "name": "clients",
            "endpoint": {
                "path": "/v1/clients",
                "data_selector": "clients",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "not response.get('links', {}).get('next', {})"
                }
            }
        },
        {
            "name": "company",
            "endpoint": {
                "path": "/v1/company",
                "data_selector": "data"
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "not response.get('links', {}).get('next', {})"
                }
            }
        }
    ]
})
```

# Authentication

The API supports OAuth2 authentication. The following secrets are required for authentication:

- `client_id`: The client ID of your application.
- `client_secret`: The client secret of your application.
- `refresh_token`: The refresh token used to renew the access token.
- `token_url`: The URL endpoint for token refresh.

# Resources

## Clients

- **Endpoint Path**: `/v1/clients`
- **Data Selector**: `clients`
- **Pagination**: Cursor-based with `next` as the cursor parameter.
- **Primary Key**: `id`

## Company

- **Endpoint Path**: `/v1/company`
- **Data Selector**: `data`
- **Primary Key**: Not specified

## Contacts

- **Endpoint Path**: `/v1/contacts`
- **Data Selector**: `contacts`
- **Pagination**: Cursor-based with `next` as the cursor parameter.
- **Primary Key**: `id`

# Error Handling

The API includes error handling with retry logic for specific HTTP status codes:

- **Retry on Status Codes**: 401, 403, 404
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to maintain vendor neutrality. For example, `client_id` is used instead of any branded identifier.