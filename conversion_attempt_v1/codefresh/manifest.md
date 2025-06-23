# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization for various resources such as accounts, builds, and analytics metadata. The configuration is designed to be vendor-neutral and follows dltHub's REST API source format.

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
            "name": "accounts",
            "endpoint": {
                "path": "/accounts/{account_id}/users",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "account_settings",
            "endpoint": {
                "path": "/account-settings",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "agents",
            "endpoint": {
                "path": "/agents",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "builds",
            "endpoint": {
                "path": "/workflow",
                "data_selector": "workflows.docs",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "audit",
            "endpoint": {
                "path": "/audit",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "pageSize",
                    "limit": 1000
                }
            }
        },
        {
            "name": "analytics_metadata",
            "endpoint": {
                "path": "/analytics/metadata",
                "data_selector": "reports.*",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "analytics_reports",
            "endpoint": {
                "path": "/analytics/reports/{report_name}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "execution_contexts",
            "endpoint": {
                "path": "/execution-contexts",
                "data_selector": "docs",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "contexts",
            "endpoint": {
                "path": "/contexts",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/projects",
                "data_selector": "projects",
                "paginator": {
                    "type": "offset",
                    "offset_param": "offset",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "pipelines",
            "endpoint": {
                "path": "/pipelines",
                "data_selector": "docs",
                "paginator": {
                    "type": "offset",
                    "offset_param": "offset",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "step_types",
            "endpoint": {
                "path": "/step-types",
                "data_selector": "docs.*",
                "paginator": {
                    "type": "offset",
                    "offset_param": "offset",
                    "limit_param": "limit",
                    "limit": 1000
                }
            }
        },
        {
            "name": "helm_repos",
            "endpoint": {
                "path": "/helm/repos",
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
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`
- **Location**: Header

# Resources

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path and Method**: Each resource has a specific path and uses the GET method
- **Pagination Strategy**: Various strategies including `single_page`, `page_number`, and `offset`
- **Data Selector**: Specifies the path to extract data from the API response

# Error Handling

- **Retry/Backoff Logic**: Not explicitly defined in the YAML, but can be configured in dltHub
- **Known HTTP Response Codes**: Handle standard HTTP errors like 429, 500, etc.
- **Fallback/Handling Behavior**: Generalized error handling can be implemented

# Schema

- **Fields, Types, Nullability**: Defined in the YAML schema section, use neutral field names
- **Example**: `id`, `created`, `metadata`, etc.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. It abstracts away any proprietary details and focuses on the reusable patterns for open-source publication or smart assistant training.