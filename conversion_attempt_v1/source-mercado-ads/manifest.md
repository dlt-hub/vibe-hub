# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for the retrieval of advertising analytics data from various placements, including brand, display, and product advertisements. The configuration supports OAuth2 authentication and includes pagination and error handling strategies.

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
            "token_url": "https://api.example.com/oauth/token",
            "scopes": ["offline_access", "read"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "advertisers",
            "endpoint": {
                "path": "/v1/advertisers",
                "data_selector": "advertisers",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/advertisers/{advertiser_id}/campaigns",
                "data_selector": "campaigns",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "metrics",
            "endpoint": {
                "path": "/v1/campaigns/{campaign_id}/metrics",
                "data_selector": "metrics",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: `dlt.secrets["client_id"]`
- **Client Secret**: `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/oauth/token`
- **Scopes**: `["offline_access", "read"]`

# Resources

### Advertisers
- **Endpoint Path**: `/v1/advertisers`
- **Data Selector**: `advertisers`
- **Pagination**: Offset-based with `limit` and `offset` parameters

### Campaigns
- **Endpoint Path**: `/v1/advertisers/{advertiser_id}/campaigns`
- **Data Selector**: `campaigns`
- **Pagination**: Offset-based with `limit` and `offset` parameters

### Metrics
- **Endpoint Path**: `/v1/campaigns/{campaign_id}/metrics`
- **Data Selector**: `metrics`
- **Pagination**: Offset-based with `limit` and `offset` parameters

# Error Handling

- **Retry on Status Codes**: [401, 404]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with fields such as `advertiser_id`, `campaign_id`, `date`, and other relevant metrics. Field types include strings, numbers, and nullable types. The schema is designed to be flexible and accommodate various data types returned by the API.