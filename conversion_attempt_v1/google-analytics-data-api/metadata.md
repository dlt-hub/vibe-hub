# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various data resources, allowing for efficient data extraction and synchronization.

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
            "token_url": "https://oauth2.googleapis.com/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "website_overview",
            "endpoint": {
                "path": "/v1/website_overview",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "daily_active_users",
            "endpoint": {
                "path": "/v1/daily_active_users",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "traffic_sources",
            "endpoint": {
                "path": "/v1/traffic_sources",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
        // Additional resources can be added here
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: Retrieved from `dlt.secrets["client_id"]`
- **Client Secret**: Retrieved from `dlt.secrets["client_secret"]`
- **Token URL**: `https://oauth2.googleapis.com/token`

# Resources

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Generalized to `/v1/resource_name`
- **Pagination Strategy**: Offset-based with a limit of 100
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implemented with exponential backoff
- **HTTP Status Codes**: Retries on 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Fields**: Use neutral field names such as `analytics_id`, `user_key`, etc.
- **Types**: Consistent with the data structure provided by the API
- **Nullability**: Defined based on the API's response schema

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. It abstracts away any proprietary or branded content, ensuring a generic and reusable integration pattern.