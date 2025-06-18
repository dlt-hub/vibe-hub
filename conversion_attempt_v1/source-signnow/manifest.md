# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and configuration settings. The API allows for data retrieval through different endpoints, supporting both full and incremental synchronization based on datetime cursors.

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
            "api_key": dlt.secrets["api_key_id"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "user",
            "endpoint": {
                "path": "/user",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "user_modified_documents",
            "endpoint": {
                "path": "/user/documentsv2",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "crm_contacts",
            "endpoint": {
                "path": "/v2/crm/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                }
            }
        },
        {
            "name": "signing_links",
            "endpoint": {
                "path": "/v2/application/signing-links",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key` for most resources, `basic` for the `signing_links` resource.
- **Secrets**: 
  - `api_key_id`: Used for API key authentication.
  - `auth_token`: Used for basic authentication in the `signing_links` resource.

# Resources

- **Resource Name**: Abstracted from branded terms.
- **Endpoint Path**: Generalized paths for each resource.
- **Pagination Strategy**: 
  - `single_page` for resources without pagination.
  - `page_number` for resources with page-based pagination, using `page` and `per_page` parameters.
- **Data Selector**: Typically set to `"data"` to extract the relevant data from the API response.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 for retries.
- **Timeouts**: Handle timeouts and data-limit messages gracefully.

# Schema

- **Fields**: Use neutral field names such as `customer_id` or `user_key`.
- **Types and Nullability**: Ensure consistency with the provided YAML schema, using generalized field names and types.