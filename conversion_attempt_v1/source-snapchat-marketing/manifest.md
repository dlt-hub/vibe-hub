# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with specific configurations for data retrieval, pagination, and error handling.

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
            "token_url": "https://api.example.com/oauth2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v1/me/organizations",
                "data_selector": "organizations.*.organization",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('paging', {}).get('next_link', {}) }}",
                    "stop_condition": "{{ not response.get('paging', {}).get('next_link', {}) }}"
                }
            }
        },
        {
            "name": "adaccounts",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}/adaccounts",
                "data_selector": "adaccounts.*.adaccount",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('paging', {}).get('next_link', {}) }}",
                    "stop_condition": "{{ not response.get('paging', {}).get('next_link', {}) }}"
                }
            }
        },
        {
            "name": "creatives",
            "endpoint": {
                "path": "/v1/adaccounts/{adaccount_id}/creatives",
                "data_selector": "creatives.*.creative",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('paging', {}).get('next_link', {}) }}",
                    "stop_condition": "{{ not response.get('paging', {}).get('next_link', {}) }}"
                }
            }
        },
        {
            "name": "ads",
            "endpoint": {
                "path": "/v1/adaccounts/{adaccount_id}/ads",
                "data_selector": "ads.*.ad",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('paging', {}).get('next_link', {}) }}",
                    "stop_condition": "{{ not response.get('paging', {}).get('next_link', {}) }}"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`
  - `client_secret`
  - `refresh_token`
- **Token URL**: `https://api.example.com/oauth2/token`

# Resources

### Organizations
- **Endpoint Path**: `/v1/me/organizations`
- **Data Selector**: `organizations.*.organization`
- **Pagination**: Cursor-based

### Ad Accounts
- **Endpoint Path**: `/v1/organizations/{organization_id}/adaccounts`
- **Data Selector**: `adaccounts.*.adaccount`
- **Pagination**: Cursor-based

### Creatives
- **Endpoint Path**: `/v1/adaccounts/{adaccount_id}/creatives`
- **Data Selector**: `creatives.*.creative`
- **Pagination**: Cursor-based

### Ads
- **Endpoint Path**: `/v1/adaccounts/{adaccount_id}/ads`
- **Data Selector**: `ads.*.ad`
- **Pagination**: Cursor-based

# Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 403
  - Max retries: 3
  - Backoff strategy: Exponential with factor 1

# Schema

Each resource has a schema that defines the fields, types, and nullability. Field names are generalized to ensure vendor neutrality. For example, `organization_id` is used instead of any branded term.