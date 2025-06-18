# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization from various resources, allowing for efficient data extraction and management.

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
            "name": "accounts",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "account_users",
            "endpoint": {
                "path": "/v1/account_users",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "ad_campaign_analytics",
            "endpoint": {
                "path": "/v1/ad_campaign_analytics",
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
  - `client_id`: Your OAuth client ID
  - `client_secret`: Your OAuth client secret
- **Token URL**: `https://api.example.com/oauth/token`

## Resources

### Accounts
- **Endpoint Path**: `/v1/accounts`
- **Pagination**: Offset-based with a limit of 100
- **Data Selector**: `data`

### Account Users
- **Endpoint Path**: `/v1/account_users`
- **Pagination**: Offset-based with a limit of 100
- **Data Selector**: `data`

### Ad Campaign Analytics
- **Endpoint Path**: `/v1/ad_campaign_analytics`
- **Pagination**: Offset-based with a limit of 100
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: [429, 500, 502, 503, 504]
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - `id`: Primary key for resources
  - Use neutral field names such as `analytics_id` for analytics-related data

This configuration ensures a clean and efficient integration with the third-party analytics API, following dltHub's REST API source format.