# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using the dltHub REST API source format. The integration allows for seamless data synchronization between the API and various destinations, facilitating data analysis and reporting. It supports both incremental and full data syncs, offering flexibility in data synchronization.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_token"],
            "password": dlt.secrets["api_secret"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "definitions",
            "endpoint": {
                "path": "/{workspace}/definitions",
                "data_selector": "_embedded.definitions"
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/{workspace}/groups",
                "data_selector": "_embedded.groups"
            }
        },
        {
            "name": "memberships",
            "endpoint": {
                "path": "/{workspace}/memberships",
                "data_selector": "_embedded.memberships"
            }
        },
        {
            "name": "spaces",
            "endpoint": {
                "path": "/{workspace}/spaces",
                "data_selector": "_embedded.spaces"
            }
        },
        {
            "name": "space_memberships",
            "endpoint": {
                "path": "/{workspace}/spaces/{space_token}/memberships",
                "data_selector": "_embedded.space_memberships"
            }
        },
        {
            "name": "groups_memberships",
            "endpoint": {
                "path": "/{workspace}/groups/{group_token}/memberships",
                "data_selector": "_embedded.group_memberships"
            }
        },
        {
            "name": "data_sources",
            "endpoint": {
                "path": "/{workspace}/data_sources",
                "data_selector": "_embedded.data_sources"
            }
        },
        {
            "name": "reports",
            "endpoint": {
                "path": "/{workspace}/data_sources/{data_source_token}/reports",
                "data_selector": "_embedded.reports"
            }
        },
        {
            "name": "report_runs",
            "endpoint": {
                "path": "/{workspace}/reports/{report_token}/runs",
                "data_selector": "_embedded.report_runs",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "queries",
            "endpoint": {
                "path": "/{workspace}/reports/{report_token}/queries",
                "data_selector": "_embedded.queries"
            }
        },
        {
            "name": "query_runs",
            "endpoint": {
                "path": "/{workspace}/reports/{report_token}/runs/{run_token}/query_runs",
                "data_selector": "_embedded.query_runs"
            }
        },
        {
            "name": "charts",
            "endpoint": {
                "path": "/{workspace}/reports/{report_token}/queries/{query_token}/charts",
                "data_selector": "_embedded.charts"
            }
        },
        {
            "name": "report_filters",
            "endpoint": {
                "path": "/{workspace}/reports/{report_token}/filters",
                "data_selector": "_embedded.report_filters"
            }
        },
        {
            "name": "datasets",
            "endpoint": {
                "path": "/{workspace}/spaces/{space_token}/datasets",
                "data_selector": "_embedded.reports"
            }
        },
        {
            "name": "datasets_runs",
            "endpoint": {
                "path": "/{workspace}/datasets/{dataset_token}/runs",
                "data_selector": "_embedded.report_runs",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "field_descriptions",
            "endpoint": {
                "path": "/{workspace}/datasets/{dataset_token}/field_descriptions",
                "data_selector": "_embedded.field_descriptions",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "report_schedules",
            "endpoint": {
                "path": "/{workspace}/reports/{report_token}/schedules",
                "data_selector": "_embedded.report_schedules"
            }
        },
        {
            "name": "reports_subscriptions",
            "endpoint": {
                "path": "/{workspace}/reports/{report_token}/subscriptions",
                "data_selector": "_embedded.report_subscriptions"
            }
        },
        {
            "name": "datasets_schedules",
            "endpoint": {
                "path": "/{workspace}/datasets/{dataset_token}/schedules",
                "data_selector": "_embedded.report_schedules"
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_token`: Used as the username
  - `api_secret`: Used as the password

# Resources

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Generalized paths using placeholders for dynamic segments
- **Pagination Strategy**: 
  - Page number pagination for resources with paginated data
- **Data Selector**: Extracts data from the specified field path

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.

# Schema

- **Fields**: Abstracted field names such as `id`, `token`, `name`, etc.
- **Types**: Consistent with the YAML schema definitions
- **Nullability**: Fields may be nullable as specified in the schema

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format, ensuring flexibility and ease of use in data synchronization tasks.