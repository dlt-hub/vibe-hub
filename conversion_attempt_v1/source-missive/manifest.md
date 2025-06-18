# Overview

This document provides a configuration guide for integrating with a third-party REST API using dltHub's REST API source format. The integration supports various resources, each with its own endpoint and pagination strategy. Authentication is handled via an API key, and error handling includes retry logic for rate-limited responses.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
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
            "name": "contact_books",
            "endpoint": {
                "path": "/v1/contact_books",
                "data_selector": "contact_books",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "contact_groups",
            "endpoint": {
                "path": "/v1/contact_groups",
                "data_selector": "contact_groups",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "users",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/v1/teams",
                "data_selector": "teams",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "shared_labels",
            "endpoint": {
                "path": "/v1/shared_labels",
                "data_selector": "shared_labels",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v1/organizations",
                "data_selector": "organizations",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "conversations",
            "endpoint": {
                "path": "/v1/conversations",
                "data_selector": "conversations",
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **API Key**: Stored securely in `dlt.secrets["api_key"]`
- **Location**: Header

# Resources

## contact_books
- **Endpoint Path**: `/v1/contact_books`
- **Data Selector**: `contact_books`
- **Pagination**: Offset with limit 50

## contacts
- **Endpoint Path**: `/v1/contacts`
- **Data Selector**: `contacts`
- **Pagination**: Offset with limit 50

## contact_groups
- **Endpoint Path**: `/v1/contact_groups`
- **Data Selector**: `contact_groups`
- **Pagination**: Offset with limit 50

## users
- **Endpoint Path**: `/v1/users`
- **Data Selector**: `users`
- **Pagination**: Offset with limit 50

## teams
- **Endpoint Path**: `/v1/teams`
- **Data Selector**: `teams`
- **Pagination**: Offset with limit 50

## shared_labels
- **Endpoint Path**: `/v1/shared_labels`
- **Data Selector**: `shared_labels`
- **Pagination**: Offset with limit 50

## organizations
- **Endpoint Path**: `/v1/organizations`
- **Data Selector**: `organizations`
- **Pagination**: Offset with limit 50

## conversations
- **Endpoint Path**: `/v1/conversations`
- **Data Selector**: `conversations`
- **Pagination**: Not applicable

# Error Handling

- **Retry Logic**: Retries on HTTP status code 429
- **Max Retries**: 3
- **Backoff Factor**: 2 (Exponential backoff)

# Schema

Each resource has a schema with fields such as `id`, `name`, and other relevant attributes. Fields are defined with types and nullability, ensuring data consistency and integrity.