# Overview

This document provides a configuration example for integrating with a third-party communication API using dltHub's REST API source format. The integration supports full refresh sync types and is designed to handle various resources available through the API.

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
            "name": "user_business_hours",
            "endpoint": {
                "path": "/v1/user_business_hours",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "company_business_hours",
            "endpoint": {
                "path": "/v1/company_business_hours",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "forwarding_numbers",
            "endpoint": {
                "path": "/v1/forwarding_numbers",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "blocked_allowed_phonenumbers",
            "endpoint": {
                "path": "/v1/blocked_allowed_phonenumbers",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "user_call_records",
            "endpoint": {
                "path": "/v1/user_call_records",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "user_active_calls",
            "endpoint": {
                "path": "/v1/user_active_calls",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "call_monitoring_groups",
            "endpoint": {
                "path": "/v1/call_monitoring_groups",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "call_queues",
            "endpoint": {
                "path": "/v1/call_queues",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "ivr_prompts",
            "endpoint": {
                "path": "/v1/ivr_prompts",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of each request.

# Resources

Each resource corresponds to a specific endpoint of the API. The resources are configured to perform full refresh syncs, retrieving all available data in a single request.

- **Resource Names**: Abstracted to generic terms such as `user_business_hours`, `company_business_hours`, etc.
- **Endpoint Paths**: Generalized paths like `/v1/user_business_hours`.
- **Pagination Strategy**: All resources use a `single_page` pagination strategy, indicating that data is retrieved in a single request without the need for pagination.

# Error Handling

The configuration does not specify error handling logic. It is recommended to implement retry and backoff strategies for handling transient errors and HTTP status codes like 429 (Too Many Requests) or 500 (Internal Server Error).

# Schema

The schema for each resource is not explicitly defined in the configuration. It is recommended to use neutral field names and types consistent with the data returned by the API.