# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with specific endpoints and pagination strategies. The API offers advanced data retrieval capabilities, including incremental synchronization based on datetime cursors.

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
            "name": "audit_logs",
            "endpoint": {
                "path": "/v1/audit_logs",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "autotunes",
            "endpoint": {
                "path": "/v1/autotunes",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "dynamic_configs",
            "endpoint": {
                "path": "/v1/dynamic_configs",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "dynamic_configs_versions",
            "endpoint": {
                "path": "/v1/dynamic_configs/{dynamic_config_id}/versions",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "dynamic_configs_rules",
            "endpoint": {
                "path": "/v1/dynamic_configs/{dynamic_config_id}/rules",
                "data_selector": "data.*.rules.*",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "events_metrics",
            "endpoint": {
                "path": "/v1/events/{event_name}/metrics",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "experiments",
            "endpoint": {
                "path": "/v1/experiments",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "gates",
            "endpoint": {
                "path": "/v1/gates",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "gates_rules",
            "endpoint": {
                "path": "/v1/gates/{gate_id}/rules",
                "data_selector": "data.*.rules.*",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "holdouts",
            "endpoint": {
                "path": "/v1/holdouts",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "ingestion_status",
            "endpoint": {
                "path": "/v1/ingestion/status",
                "data_selector": "data"
            }
        },
        {
            "name": "ingestion_runs",
            "endpoint": {
                "path": "/v1/ingestion/runs",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "layers",
            "endpoint": {
                "path": "/v1/layers",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "metrics",
            "endpoint": {
                "path": "/v1/metrics/list",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "metrics_values",
            "endpoint": {
                "path": "/v1/metrics/values",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/v1/segments",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "segments_ids",
            "endpoint": {
                "path": "/v1/segments/{segment_id}/id_list",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/tags",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "target_apps",
            "endpoint": {
                "path": "/v1/target_app",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header.

# Resources

Each resource corresponds to a specific endpoint and supports pagination using a page number strategy. The data is selected from the "data" field in the response.

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized paths based on the original YAML configuration.
- **Pagination Strategy**: Page number with a limit of 100 items per page, starting from page 1.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 for retries.
- **Max Retries**: Set a maximum of 3 retries for failed requests.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for each resource is typically "id", and additional fields are included as per the original YAML schema definitions.