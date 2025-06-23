# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and data extraction logic. The configuration is designed to be vendor-neutral and does not include any proprietary or branded content.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/",
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
            "name": "audit_logs",
            "endpoint": {
                "path": "/v2/audit/events",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page[cursor]",
                    "cursor_path": "meta.page.after"
                }
            }
        },
        {
            "name": "dashboards",
            "endpoint": {
                "path": "/v1/dashboard",
                "data_selector": "dashboards"
            }
        },
        {
            "name": "downtimes",
            "endpoint": {
                "path": "/v1/downtime",
                "data_selector": []
            }
        },
        {
            "name": "incident_teams",
            "endpoint": {
                "path": "/v2/teams",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.next_offset"
                }
            }
        },
        {
            "name": "incidents",
            "endpoint": {
                "path": "/v2/incidents",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page[offset]",
                    "cursor_path": "meta.pagination.next_offset"
                }
            }
        },
        {
            "name": "logs",
            "endpoint": {
                "path": "/v2/logs/events",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "links.next"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "metrics",
            "endpoint": {
                "path": "/v2/metrics",
                "data_selector": "data"
            }
        },
        {
            "name": "monitors",
            "endpoint": {
                "path": "/v1/monitor",
                "data_selector": []
            }
        },
        {
            "name": "service_level_objectives",
            "endpoint": {
                "path": "/v1/slo",
                "data_selector": "data"
            }
        },
        {
            "name": "synthetic_tests",
            "endpoint": {
                "path": "/v1/synthetics/tests",
                "data_selector": "tests"
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v2/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: 
  - `api_key`: The API key used for authentication.
  - `application_key`: An additional key required for accessing certain resources.

# Resources

Each resource is defined with a specific endpoint path and data extraction logic. The configuration includes pagination strategies where applicable, using cursor or page number pagination.

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: Generalized to `/v1/resource` or similar
- **HTTP Method**: Default is `GET`
- **Data Selector**: Specifies the JSON path to extract data
- **Pagination Strategy**: Includes cursor and page number pagination

# Error Handling

The configuration includes error handling logic for retrying requests on specific HTTP status codes, such as 429 (Too Many Requests). The retry logic uses an exponential backoff strategy with a maximum of 3 retries.

# Schema

The schema for each resource is defined with neutral field names and types. The configuration does not include any proprietary or branded schema details.