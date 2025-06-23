# Overview

This document provides a configuration setup for integrating with a third-party API that transforms contract data into financial insights. The API allows users to sign, store, and search contracts. The API is accessible from both sandbox and production environments.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "X-API-KEY",
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
            "name": "agreements",
            "endpoint": {
                "path": "/organizations/{parent_id}/agreements",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "start",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "user_organizations",
            "endpoint": {
                "path": "/user/me/organizations",
                "data_selector": "organizations"
            }
        },
        {
            "name": "organization",
            "endpoint": {
                "path": "/organizations/{parent_id}",
                "data_selector": "data"
            }
        },
        {
            "name": "folders",
            "endpoint": {
                "path": "/organizations/{parent_id}/folders",
                "data_selector": "data"
            }
        },
        {
            "name": "reports",
            "endpoint": {
                "path": "/organizations/{parent_id}/reports",
                "data_selector": "reports",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "numberOfItemsByPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/tags",
                "data_selector": "tags"
            }
        },
        {
            "name": "organization_members",
            "endpoint": {
                "path": "/organizations/{parent_id}/members",
                "data_selector": "members",
                "paginator": {
                    "type": "offset",
                    "offset_param": "start",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Header**: The API key is included in the request header as `X-API-KEY`.

# Resources

- **agreements**: Access agreements data with pagination using page numbers.
- **user_organizations**: Retrieve user organization data.
- **organization**: Fetch organization details.
- **folders**: Access folder data within an organization.
- **reports**: Retrieve reports with pagination using page numbers.
- **tags**: Access tags data.
- **organization_members**: Retrieve organization members with offset pagination.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

- **agreements**: Includes fields like `uid`, `metadata`, `status`, and `tags`.
- **user_organizations**: Contains fields such as `id`, `name`, and `subscription`.
- **organization**: Includes fields like `id`, `name`, and `region`.
- **folders**: Contains fields such as `id`, `documentCount`, and `access`.
- **reports**: Includes fields like `id`, `name`, and `description`.
- **tags**: Contains fields such as `id`, `name`, and `organizationId`.
- **organization_members**: Includes fields like `user_id`, `role`, and `organization`.

This configuration provides a comprehensive setup for accessing and managing data from a third-party API, ensuring a seamless integration experience.