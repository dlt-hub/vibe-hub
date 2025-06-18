# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and schema. The API uses bearer token authentication and supports error handling with retry logic for rate-limited responses.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
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
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "billing_usage",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/billing/usage",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "resources",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/permissions/resources",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "sending_domains",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/sending_domains",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "inboxes",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/inboxes",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "messages",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/inboxes/{inbox_id}/messages",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/projects",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_token"]`

# Resources

Each resource is defined with a unique name, endpoint path, and error handling configuration. The data selector is set to extract data from the response.

- **accounts**: `/v1/accounts`
- **billing_usage**: `/v1/accounts/{account_id}/billing/usage`
- **resources**: `/v1/accounts/{account_id}/permissions/resources`
- **sending_domains**: `/v1/accounts/{account_id}/sending_domains`
- **inboxes**: `/v1/accounts/{account_id}/inboxes`
- **messages**: `/v1/accounts/{account_id}/inboxes/{inbox_id}/messages`
- **projects**: `/v1/accounts/{account_id}/projects`

# Error Handling

The error handling strategy includes retry logic for HTTP status code 429 (rate-limited responses). The configuration specifies a maximum of 2 retries with a constant backoff factor of 5 seconds.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for each resource is specified as "id" or "uuid" where applicable. The schemas are designed to be flexible, allowing for additional properties.