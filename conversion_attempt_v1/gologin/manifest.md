# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various data resources, each with its own endpoint and configuration settings. The API allows for data retrieval with pagination and supports incremental synchronization based on datetime fields.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "profiles",
            "endpoint": {
                "path": "/v1/profiles",
                "data_selector": "profiles",
                "paginator": {
                    "type": "offset",
                    "limit": 30,
                    "offset_param": "page"
                }
            }
        },
        {
            "name": "browser_history",
            "endpoint": {
                "path": "/v1/browser/{br_id}/history",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 30,
                    "offset_param": "page"
                }
            }
        },
        {
            "name": "browser_cookies",
            "endpoint": {
                "path": "/v1/browser/{br_id}/cookies",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 30,
                    "offset_param": "page"
                }
            }
        },
        {
            "name": "folders",
            "endpoint": {
                "path": "/v1/folders",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 30,
                    "offset_param": "page"
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/tags/all",
                "data_selector": "tags",
                "paginator": {
                    "type": "offset",
                    "limit": 30,
                    "offset_param": "page"
                }
            }
        },
        {
            "name": "user",
            "endpoint": {
                "path": "/v1/user",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 30,
                    "offset_param": "page"
                }
            }
        },
        {
            "name": "user_metadata",
            "endpoint": {
                "path": "/v1/user/metadata",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 30,
                    "offset_param": "page"
                }
            }
        },
        {
            "name": "user_balance",
            "endpoint": {
                "path": "/v1/user/balance",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 30,
                    "offset_param": "page"
                }
            }
        },
        {
            "name": "user_timezones",
            "endpoint": {
                "path": "/v1/timezone",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 30,
                    "offset_param": "page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

Each resource is configured with a specific endpoint path and supports pagination using an offset strategy. The data selector specifies the field path to extract data from the API response.

- **Profiles**: `/v1/profiles`
- **Browser History**: `/v1/browser/{br_id}/history`
- **Browser Cookies**: `/v1/browser/{br_id}/cookies`
- **Folders**: `/v1/folders`
- **Tags**: `/v1/tags/all`
- **User**: `/v1/user`
- **User Metadata**: `/v1/user/metadata`
- **User Balance**: `/v1/user/balance`
- **User Timezones**: `/v1/timezone`

# Error Handling

The configuration includes retry logic for handling HTTP errors. The system will retry requests on specific status codes with a backoff strategy.

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for each resource is specified to ensure data integrity.

- **Profiles**: Fields include `id`, `updatedAt`, and other profile-related attributes.
- **Browser History**: Fields include `id`, `lastModified`, and `size`.
- **Browser Cookies**: Fields include `uuid`, `creationDate`, and cookie-related attributes.
- **Folders**: Fields include `id`, `name`, and `shared`.
- **Tags**: Fields include `_id`, `color`, and `title`.
- **User**: Fields include `_id`, `createdAt`, and user-related attributes.
- **User Metadata**: Fields include `_id`, `createdAt`, and metadata-related attributes.
- **User Balance**: Fields include `uuid`, `available`, and balance-related attributes.
- **User Timezones**: Fields include `uuid`, `accuracy`, and timezone-related attributes.