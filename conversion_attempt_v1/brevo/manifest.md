# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with specific endpoints and configurations. The API uses an API key for authentication and supports pagination and error handling strategies.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "contacts_attributes",
            "endpoint": {
                "path": "/v1/contacts/attributes",
                "data_selector": "attributes",
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "contacts_folders_lists",
            "endpoint": {
                "path": "/v1/contacts/folders/{folderId}/lists",
                "data_selector": "lists",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/v1/companies",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 500,
                    "page_param": "page"
                },
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

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`
- **Location**: Header

# Resources

### Contacts
- **Endpoint Path**: `/v1/contacts`
- **Pagination Strategy**: Offset
- **Data Selector**: `contacts`
- **Primary Key**: `id`

### Contacts Attributes
- **Endpoint Path**: `/v1/contacts/attributes`
- **Data Selector**: `attributes`

### Contacts Folders Lists
- **Endpoint Path**: `/v1/contacts/folders/{folderId}/lists`
- **Pagination Strategy**: Offset
- **Data Selector**: `lists`
- **Primary Key**: `id`

### Companies
- **Endpoint Path**: `/v1/companies`
- **Pagination Strategy**: Page Number
- **Data Selector**: `items`
- **Primary Key**: `id`

# Error Handling

- **Retry Logic**: Retry on HTTP status code 429
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Fields**: Use neutral field names such as `id`, `modifiedAt`, `attributes`, etc.
- **Types**: Include types like `string`, `number`, `boolean`, and `array` as applicable.
- **Nullability**: Fields can be nullable, indicated by including `"null"` in the type array.