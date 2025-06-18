# Overview

This document provides a configuration example for integrating with a third-party CRM API using the dltHub REST API source. The integration allows for data extraction from various resources such as contacts, users, companies, and more. The API supports pagination and requires authentication via basic HTTP credentials.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3/",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
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
                "path": "/contacts",
                "data_selector": "data.contacts.*.contact",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "data.*.user",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "bootstrap",
            "endpoint": {
                "path": "/bootstrap",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/companies",
                "data_selector": "data.companies.*.company",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "actions",
            "endpoint": {
                "path": "/actions",
                "data_selector": "data.actions.*.action",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "deals",
            "endpoint": {
                "path": "/deals",
                "data_selector": "data.deals.*.deal",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "notes",
            "endpoint": {
                "path": "/notes",
                "data_selector": "data.notes.*.note",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "relationship_types",
            "endpoint": {
                "path": "/relationship_types",
                "data_selector": "data.relationship_types.*.relationship_type",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "pipelines",
            "endpoint": {
                "path": "/pipelines",
                "data_selector": "data.pipelines.*.pipeline",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "statuses",
            "endpoint": {
                "path": "/statuses",
                "data_selector": "data.*.status",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "lead_sources",
            "endpoint": {
                "path": "/lead_sources",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "filters",
            "endpoint": {
                "path": "/filters",
                "data_selector": "data.filters.*.filter",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "predefined_actions",
            "endpoint": {
                "path": "/predefined_actions",
                "data_selector": "data.predefined_actions.*.predefined_action",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "predefined_items",
            "endpoint": {
                "path": "/predefined_items",
                "data_selector": "data.predefined_items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/custom_fields",
                "data_selector": "data.custom_fields.*.custom_field",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "calls",
            "endpoint": {
                "path": "/calls",
                "data_selector": "data.calls.*.call",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "meetings",
            "endpoint": {
                "path": "/meetings",
                "data_selector": "data.meetings.*.meeting",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic HTTP
- **Secrets**: 
  - `username`: The user ID for API access.
  - `password`: The API key for authentication.

# Resources

Each resource is configured with a specific endpoint path, data selector, and pagination strategy. The primary key for each resource is typically the `id` field, and the default HTTP method is GET.

# Error Handling

The configuration does not explicitly define error handling strategies such as retry logic or specific HTTP status code handling. It is recommended to implement these as needed based on the API's behavior and requirements.

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to ensure vendor neutrality. For example, fields like `id`, `name`, and `created_at` are common across resources.