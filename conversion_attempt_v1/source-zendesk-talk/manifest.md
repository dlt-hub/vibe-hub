# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and configuration. The API uses a combination of API key and OAuth2.0 authentication methods, and supports pagination and error handling strategies.

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
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "addresses",
            "endpoint": {
                "path": "/v1/addresses",
                "data_selector": "addresses.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page",
                    "stop_condition": "not response.get('next_page', {})"
                }
            }
        },
        {
            "name": "agents_activity",
            "endpoint": {
                "path": "/v1/stats/agents_activity",
                "data_selector": "agents_activity.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page",
                    "stop_condition": "not response.get('next_page', {})"
                }
            }
        },
        {
            "name": "agents_overview",
            "endpoint": {
                "path": "/v1/stats/agents_overview",
                "data_selector": "agents_overview"
            }
        },
        {
            "name": "greeting_categories",
            "endpoint": {
                "path": "/v1/greeting_categories",
                "data_selector": "greeting_categories.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page",
                    "stop_condition": "not response.get('next_page', {})"
                }
            }
        },
        {
            "name": "greetings",
            "endpoint": {
                "path": "/v1/greetings",
                "data_selector": "greetings.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page",
                    "stop_condition": "not response.get('next_page', {})"
                }
            }
        },
        {
            "name": "phone_numbers",
            "endpoint": {
                "path": "/v1/phone_numbers",
                "data_selector": "phone_numbers.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page",
                    "stop_condition": "not response.get('next_page', {})"
                }
            }
        },
        {
            "name": "call_legs",
            "endpoint": {
                "path": "/v1/stats/incremental/legs",
                "data_selector": "legs.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page",
                    "stop_condition": "response.get('count', {}) <= 1"
                }
            }
        },
        {
            "name": "calls",
            "endpoint": {
                "path": "/v1/stats/incremental/calls",
                "data_selector": "calls.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page",
                    "stop_condition": "response.get('count', {}) <= 1"
                }
            }
        },
        {
            "name": "current_queue_activity",
            "endpoint": {
                "path": "/v1/stats/current_queue_activity",
                "data_selector": "current_queue_activity"
            }
        },
        {
            "name": "account_overview",
            "endpoint": {
                "path": "/v1/stats/account_overview",
                "data_selector": "account_overview"
            }
        },
        {
            "name": "ivrs",
            "endpoint": {
                "path": "/v1/ivr",
                "data_selector": "ivrs.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page",
                    "stop_condition": "not response.get('next_page', {})"
                }
            }
        },
        {
            "name": "ivr_menus",
            "endpoint": {
                "path": "/v1/ivr",
                "data_selector": "ivrs.menus.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page",
                    "stop_condition": "not response.get('next_page', {})"
                }
            }
        },
        {
            "name": "ivr_routes",
            "endpoint": {
                "path": "/v1/ivr",
                "data_selector": "ivrs.routes.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "next_page",
                    "stop_condition": "not response.get('next_page', {})"
                }
            }
        }
    ]
})
```

# Authentication

The API supports two types of authentication:

- **API Key Authentication**: The API key is included in the query parameters.
- **OAuth2.0 Authentication**: Uses bearer tokens for authentication.

# Resources

Each resource corresponds to a specific endpoint and data structure:

- **Resource Name**: Abstracted from the original stream name.
- **Endpoint Path**: The path to the resource on the API.
- **Pagination Strategy**: Cursor-based pagination is used for most resources.
- **Data Selector**: Specifies the path to the data within the API response.

# Error Handling

The API implements a composite error handler with the following strategies:

- **Retry on Specific Status Codes**: Retries are triggered for status codes like 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Uses the `Retry-After` header to determine wait times between retries.

# Schema

The schema for each resource is defined with neutral field names and types. Fields are nullable and include common data types like strings, integers, and booleans.