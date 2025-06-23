# Overview

This document provides a configuration example for integrating with a third-party data collection API using the dltHub REST API source. The integration allows for seamless data extraction and synchronization of various resources such as forms, users, workflows, and more. This setup facilitates automated, scheduled transfers of structured data, enhancing analytics, reporting, and decision-making processes by integrating field data collection capabilities with a broader data ecosystem.

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
            "name": "forms",
            "endpoint": {
                "path": "/v1/forms",
                "data_selector": "forms",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "user",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "workflows",
            "endpoint": {
                "path": "/v1/workflows",
                "data_selector": "workflows",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/v1/webhooks",
                "data_selector": "webhooks",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "changesets",
            "endpoint": {
                "path": "/v1/changesets",
                "data_selector": "changesets",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "records",
            "endpoint": {
                "path": "/v1/records",
                "data_selector": "records",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "signatures",
            "endpoint": {
                "path": "/v1/signatures",
                "data_selector": "signatures",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects",
                "data_selector": "projects",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "layers",
            "endpoint": {
                "path": "/v1/layers",
                "data_selector": "layers",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "classification_sets",
            "endpoint": {
                "path": "/v1/classification_sets",
                "data_selector": "classification_sets",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "choice_lists",
            "endpoint": {
                "path": "/v1/choice_lists",
                "data_selector": "choice_lists",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/v1/groups",
                "data_selector": "groups",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "memberships",
            "endpoint": {
                "path": "/v1/memberships",
                "data_selector": "memberships",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/v1/roles",
                "data_selector": "roles",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "authorizations",
            "endpoint": {
                "path": "/v1/authorizations",
                "data_selector": "authorizations",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "photos",
            "endpoint": {
                "path": "/v1/photos",
                "data_selector": "photos",
                "paginator": {
                    "type": "page_number",
                    "page_param": "current_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "audio",
            "endpoint": {
                "path": "/v1/audio",
                "data_selector": "audio",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
                }
            }
        },
        {
            "name": "videos",
            "endpoint": {
                "path": "/v1/videos",
                "data_selector": "videos",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20000
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

Each resource is configured with a specific endpoint path, data selector, and pagination strategy. The primary key for each resource is typically `id`, except for resources like `signatures`, `photos`, `audio`, and `videos`, which use `record_id`.

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Generalized to `/v1/resource_name`
- **Pagination Strategy**: Page number-based with parameters `page` and `per_page`, and a limit of 20000 records per page
- **Data Selector**: Extracts data from the specified field path

# Error Handling

- **Retry/Backoff Logic**: Not explicitly defined in the YAML, but can be configured as needed
- **Known HTTP Response Codes**: Not specified, but typical codes like 429, 500, 502, 503, and 504 can be handled
- **Fallback/Handling Behavior**: Generalized error handling can be implemented as per requirements

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to maintain neutrality, such as using `customer_id` instead of specific identifiers. The schema is consistent with the YAML and uses neutral field names.