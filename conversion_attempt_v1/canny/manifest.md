# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, which is included in the request body.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "body"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "boards",
            "endpoint": {
                "path": "/v1/boards/list",
                "method": "POST",
                "data_selector": "boards"
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/v1/categories/list",
                "method": "POST",
                "data_selector": "categories",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "changelog_entries",
            "endpoint": {
                "path": "/v1/entries/list",
                "method": "POST",
                "data_selector": "entries",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "comments",
            "endpoint": {
                "path": "/v1/comments/list",
                "method": "POST",
                "data_selector": "comments",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/v1/companies/list",
                "method": "POST",
                "data_selector": "companies",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "posts",
            "endpoint": {
                "path": "/v1/posts/list",
                "method": "POST",
                "data_selector": "posts",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "status_changes",
            "endpoint": {
                "path": "/v1/status_changes/list",
                "method": "POST",
                "data_selector": "statusChanges",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/tags/list",
                "method": "POST",
                "data_selector": "tags",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users/list",
                "method": "POST",
                "data_selector": "users",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "votes",
            "endpoint": {
                "path": "/v1/votes/list",
                "method": "POST",
                "data_selector": "votes",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Location**: Body
- **Key Name**: `api_key`
- **Secret Storage**: `dlt.secrets["api_key"]`

# Resources

Each resource is configured with a specific endpoint path and method. The data selector specifies the field path to extract data from the response. Pagination is handled using an offset strategy with a limit of 100 records per request.

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: `/v1/resource_name/list`
- **HTTP Method**: POST
- **Data Selector**: Field path in the response JSON
- **Pagination Strategy**: Offset with `limit` and `skip` parameters

# Error Handling

The configuration does not explicitly define error handling strategies. However, typical error handling in dltHub includes retry logic for common HTTP status codes like 429, 500, 502, 503, and 504, with a maximum number of retries and a backoff factor.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for all resources is `id`, and fields are nullable where applicable. The schema is consistent with the structure provided in the YAML configuration.