# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, which is included in the request headers.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "Api-Token",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/campaigns",
                "data_selector": "campaigns",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "lists",
            "endpoint": {
                "path": "/lists",
                "data_selector": "lists",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "deals",
            "endpoint": {
                "path": "/deals",
                "data_selector": "deals",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/segments",
                "data_selector": "segments",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "forms",
            "endpoint": {
                "path": "/forms",
                "data_selector": "forms",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: `Api-Token`
- **Location**: Header
- **Secrets**: The API key is stored securely in `dlt.secrets["api_key"]`.

# Resources

Each resource corresponds to a specific endpoint in the API. The resources are configured with the following parameters:

- **Resource Name**: A neutral name representing the data being accessed.
- **Endpoint Path**: The path to the resource on the API.
- **Data Selector**: The JSON path to extract data from the API response.
- **Pagination Strategy**: Offset-based pagination with a limit of 20 items per request.

# Error Handling

The configuration does not explicitly define error handling strategies. However, typical error handling in dltHub includes retry logic for common HTTP status codes like 429 (Too Many Requests) and 500-series server errors, with exponential backoff.

# Schema

The schema for each resource is defined in a neutral format, with fields such as `id`, `name`, and other relevant attributes. The schema is consistent with the data structure returned by the API and uses neutral field names.