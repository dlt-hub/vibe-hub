# Overview

This document provides a configuration guide for integrating with a third-party analytics API using a REST API source. The integration supports data synchronization from various resources, allowing for efficient data extraction and management.

## Configuration Example

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
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "media",
            "endpoint": {
                "path": "/v1/media",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "media_insights",
            "endpoint": {
                "path": "/v1/media_insights",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "stories",
            "endpoint": {
                "path": "/v1/stories",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "user_insights",
            "endpoint": {
                "path": "/v1/user_insights",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "story_insights",
            "endpoint": {
                "path": "/v1/story_insights",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "user_lifetime_insights",
            "endpoint": {
                "path": "/v1/user_lifetime_insights",
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

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely manage your API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Names**: media, media_insights, stories, user_insights, story_insights, users, user_lifetime_insights
- **Endpoint Paths**: Each resource has a corresponding endpoint path, such as `/v1/media` or `/v1/user_insights`.
- **Pagination Strategy**: Offset-based pagination is used with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors, such as network issues or rate limiting.
- **HTTP Response Codes**: Handle common HTTP status codes like 429 (Too Many Requests) and 500 (Internal Server Error) with appropriate retry mechanisms.
- **Backoff Strategy**: Use an exponential backoff strategy to manage retries effectively.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `timestamp`, and other relevant data points.
- **Types**: Ensure that field types are consistent with the API response, using neutral field names.
- **Nullability**: Define which fields can be nullable based on the API's data structure.

This configuration guide provides a clean and vendor-neutral approach to integrating with a third-party analytics API using dltHub's REST API source format.