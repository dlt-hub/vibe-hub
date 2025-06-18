# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various data resources, each with specific endpoints and pagination strategies. The configuration is designed to be vendor-neutral and does not include any proprietary or branded content.

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
            "token_url": "https://api.example.com/oauth2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "accounts",
            "endpoint": {
                "path": "/v1/adAccounts",
                "method": "GET",
                "params": {
                    "q": "search"
                },
                "headers": {
                    "API-Version": "202502"
                },
                "paginator": {
                    "type": "cursor",
                    "limit": 500,
                    "cursor_param": "pageToken",
                    "stop_condition": "not response.get('metadata', {}).get('nextPageToken', {})"
                }
            }
        },
        {
            "name": "account_users",
            "endpoint": {
                "path": "/v1/adAccountUsers",
                "method": "GET",
                "params": {
                    "q": "accounts",
                    "count": "500"
                },
                "headers": {
                    "API-Version": "202502"
                },
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "start"
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/adAccounts/{account_id}/adCampaigns",
                "method": "GET",
                "params": {
                    "q": "search",
                    "search": "(status:(values:List(ACTIVE,PAUSED,ARCHIVED,COMPLETED,CANCELED,DRAFT,PENDING_DELETION,REMOVED)))"
                },
                "headers": {
                    "API-Version": "202502",
                    "X-Protocol-Version": "2.0.0"
                },
                "paginator": {
                    "type": "cursor",
                    "limit": 500,
                    "cursor_param": "pageToken",
                    "stop_condition": "not response.get('metadata', {}).get('nextPageToken', {})"
                }
            }
        }
    ]
})
```

# Authentication

The integration supports OAuth2 authentication. The required credentials include:

- `client_id`: The client ID for the application.
- `client_secret`: The client secret for the application.
- `token_url`: The URL to obtain the access token.

These credentials should be stored securely using dltHub's secrets management.

# Resources

## Accounts

- **Endpoint Path**: `/v1/adAccounts`
- **HTTP Method**: GET
- **Pagination**: Cursor-based with a limit of 500 records per page.
- **Data Selector**: Extracts data from the `metadata` field.

## Account Users

- **Endpoint Path**: `/v1/adAccountUsers`
- **HTTP Method**: GET
- **Pagination**: Offset-based with a limit of 500 records per page.
- **Data Selector**: Extracts data from the `metadata` field.

## Campaigns

- **Endpoint Path**: `/v1/adAccounts/{account_id}/adCampaigns`
- **HTTP Method**: GET
- **Pagination**: Cursor-based with a limit of 500 records per page.
- **Data Selector**: Extracts data from the `metadata` field.

# Error Handling

The configuration includes error handling strategies such as retry logic and backoff strategies. The following HTTP status codes are retried:

- 429 (Too Many Requests)
- 500 (Internal Server Error)
- 502 (Bad Gateway)
- 503 (Service Unavailable)
- 504 (Gateway Timeout)

The retry logic includes an exponential backoff strategy with a factor of 5.

# Schema

The schema for each resource is defined using neutral field names. For example, the `accounts` resource includes fields such as `id`, `name`, `status`, and `created`. The schema is designed to be flexible and accommodate various data types and structures.