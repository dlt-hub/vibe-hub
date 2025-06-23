# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and configuration. The API uses an API key for authentication and supports cursor-based pagination for data retrieval.

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
            "name": "context",
            "endpoint": {
                "path": "/v2/context",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page-token",
                    "cursor_path": "next_page_token"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "self_ids",
            "endpoint": {
                "path": "/v2/me",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page-token",
                    "cursor_path": "next_page_token"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "self_collaborations",
            "endpoint": {
                "path": "/v2/me/collaborations",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page-token",
                    "cursor_path": "next_page_token"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "me",
            "endpoint": {
                "path": "/v1.1/me",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page-token",
                    "cursor_path": "next_page_token"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1.1/projects",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page-token",
                    "cursor_path": "next_page_token"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "pipelines",
            "endpoint": {
                "path": "/v2/pipeline",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page-token",
                    "cursor_path": "next_page_token"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "specific_project",
            "endpoint": {
                "path": "/v2/project/{org_id}/{project_id}",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page-token",
                    "cursor_path": "next_page_token"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "jobs",
            "endpoint": {
                "path": "/v2/project/{org_id}/{project_id}/job/{job_number}",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page-token",
                    "cursor_path": "next_page_token"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "workflow",
            "endpoint": {
                "path": "/v2/workflow/{workflow_id}",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page-token",
                    "cursor_path": "next_page_token"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "insights_metrics",
            "endpoint": {
                "path": "/v2/insights/pages/{org_id}/{project_id}/summary",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page-token",
                    "cursor_path": "next_page_token"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "insights_branches",
            "endpoint": {
                "path": "/v2/insights/{org_id}/{project_id}/branches",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page-token",
                    "cursor_path": "next_page_token"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "workflow_jobs",
            "endpoint": {
                "path": "/v2/workflow/{workflow_id}/job",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page-token",
                    "cursor_path": "next_page_token"
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

- **Type**: API Key
- **Location**: Header
- **Key Name**: `api_key`

# Resources

- **Resource Name**: context
  - **Endpoint Path**: `/v2/context`
  - **Data Selector**: `items`
  - **Pagination**: Cursor-based
  - **Primary Key**: `id`

- **Resource Name**: self_ids
  - **Endpoint Path**: `/v2/me`
  - **Data Selector**: `[]`
  - **Pagination**: Cursor-based
  - **Primary Key**: `id`

- **Resource Name**: self_collaborations
  - **Endpoint Path**: `/v2/me/collaborations`
  - **Data Selector**: `[]`
  - **Pagination**: Cursor-based
  - **Primary Key**: `id`

- **Resource Name**: me
  - **Endpoint Path**: `/v1.1/me`
  - **Data Selector**: `[]`
  - **Pagination**: Cursor-based
  - **Primary Key**: `analytics_id`

- **Resource Name**: projects
  - **Endpoint Path**: `/v1.1/projects`
  - **Data Selector**: `[]`
  - **Pagination**: Cursor-based
  - **Primary Key**: `vcs_url`

- **Resource Name**: pipelines
  - **Endpoint Path**: `/v2/pipeline`
  - **Data Selector**: `items`
  - **Pagination**: Cursor-based
  - **Primary Key**: `id`

- **Resource Name**: specific_project
  - **Endpoint Path**: `/v2/project/{org_id}/{project_id}`
  - **Data Selector**: `[]`
  - **Pagination**: Cursor-based
  - **Primary Key**: `id`

- **Resource Name**: jobs
  - **Endpoint Path**: `/v2/project/{org_id}/{project_id}/job/{job_number}`
  - **Data Selector**: `[]`
  - **Pagination**: Cursor-based
  - **Primary Key**: `number`

- **Resource Name**: workflow
  - **Endpoint Path**: `/v2/workflow/{workflow_id}`
  - **Data Selector**: `[]`
  - **Pagination**: Cursor-based
  - **Primary Key**: `id`

- **Resource Name**: insights_metrics
  - **Endpoint Path**: `/v2/insights/pages/{org_id}/{project_id}/summary`
  - **Data Selector**: `[]`
  - **Pagination**: Cursor-based
  - **Primary Key**: `project_id`

- **Resource Name**: insights_branches
  - **Endpoint Path**: `/v2/insights/{org_id}/{project_id}/branches`
  - **Data Selector**: `[]`
  - **Pagination**: Cursor-based
  - **Primary Key**: `project_id`

- **Resource Name**: workflow_jobs
  - **Endpoint Path**: `/v2/workflow/{workflow_id}/job`
  - **Data Selector**: `items`
  - **Pagination**: Cursor-based
  - **Primary Key**: `id`

# Error Handling

- **Retry on Status Codes**: [429]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Field Names**: Use neutral field names such as `analytics_id`, `project_id`, `created_at`, etc.
- **Types**: String, number, boolean, array, object
- **Nullability**: Fields can be nullable as specified in the schema.