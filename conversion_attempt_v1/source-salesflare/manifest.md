# Overview

This document provides a configuration example for integrating with a third-party CRM API using dltHub's REST API source. The integration allows for data extraction from various resources such as tasks, accounts, contacts, opportunities, workflows, and more. The API supports pagination and requires API key authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
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
            "name": "tasks",
            "endpoint": {
                "path": "/v1/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "accounts",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "opportunities",
            "endpoint": {
                "path": "/v1/opportunities",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "workflows",
            "endpoint": {
                "path": "/v1/workflows",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/tags",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "persons",
            "endpoint": {
                "path": "/v1/persons",
                "data_selector": "data"
            }
        },
        {
            "name": "email_data_sources",
            "endpoint": {
                "path": "/v1/datasources/email",
                "data_selector": "data"
            }
        },
        {
            "name": "custom_field_types",
            "endpoint": {
                "path": "/v1/customfields/types",
                "data_selector": "data"
            }
        },
        {
            "name": "pipelines",
            "endpoint": {
                "path": "/v1/pipelines",
                "data_selector": "data"
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data",
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
- **Header Name**: Authorization
- **API Key**: Stored securely in `dlt.secrets["api_key"]`

# Resources

- **Resource Names**: tasks, accounts, contacts, opportunities, workflows, tags, persons, email_data_sources, custom_field_types, pipelines, users
- **Endpoint Paths**: `/v1/tasks`, `/v1/accounts`, `/v1/contacts`, `/v1/opportunities`, `/v1/workflows`, `/v1/tags`, `/v1/persons`, `/v1/datasources/email`, `/v1/customfields/types`, `/v1/pipelines`, `/v1/users`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset-based with `offset` parameter
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Fields**: Each resource has a schema with fields such as `id`, `name`, `description`, etc.
- **Types**: Fields can be of types like string, number, boolean, object, or array
- **Nullability**: Fields can be nullable

This configuration provides a comprehensive setup for integrating with a generic CRM API using dltHub's REST API source, ensuring a clean and vendor-neutral approach.