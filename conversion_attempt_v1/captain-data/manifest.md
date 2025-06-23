# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with specific endpoints and configurations. The API requires authentication via an API key, and data retrieval is facilitated through HTTP GET requests. The configuration includes pagination and error handling strategies to ensure efficient data extraction.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "x-api-key",
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
            "name": "workspace",
            "endpoint": {
                "path": "/v3/workspace",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "workflows",
            "endpoint": {
                "path": "/v3/workflows",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "jobs",
            "endpoint": {
                "path": "/v3/workflows/{parent_id}/jobs",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "successful_jobs",
            "endpoint": {
                "path": "/v3/workflows/{parent_id}/jobs",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "job_results",
            "endpoint": {
                "path": "/v3/jobs/{parent_id}/results",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "paging.next",
                    "stop_condition": "paging.have_next_page is false"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: `x-api-key`
- **Secrets**: The API key should be stored securely and accessed via `dlt.secrets["api_key"]`.

# Resources

### Workspace
- **Endpoint Path**: `/v3/workspace`
- **Primary Key**: `name`
- **Pagination**: Single page
- **Data Selector**: `data`

### Workflows
- **Endpoint Path**: `/v3/workflows`
- **Primary Key**: `uid`
- **Pagination**: Single page
- **Data Selector**: `data`

### Jobs
- **Endpoint Path**: `/v3/workflows/{parent_id}/jobs`
- **Primary Key**: `uid`
- **Pagination**: Single page
- **Data Selector**: `data`

### Successful Jobs
- **Endpoint Path**: `/v3/workflows/{parent_id}/jobs`
- **Primary Key**: `uid`
- **Pagination**: Single page
- **Data Selector**: `data`
- **Filter Condition**: `total_row_count > 0 and status == 'finished'`

### Job Results
- **Endpoint Path**: `/v3/jobs/{parent_id}/results`
- **Primary Key**: `job_result_id`
- **Pagination**: Cursor-based
- **Data Selector**: `results`
- **Cursor Path**: `paging.next`
- **Stop Condition**: `paging.have_next_page is false`

# Error Handling

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a defined schema with fields that are nullable and of specific types. The schemas are designed to be flexible, allowing for additional properties as needed. Here are some example fields:

- **Workspace**: `current_month_end`, `email`, `name`, `tasks_left`
- **Workflows**: `created_at`, `name`, `project_id`, `steps`
- **Jobs**: `accounts`, `error_message`, `finish_time`, `status`
- **Successful Jobs**: Similar to Jobs with additional filtering
- **Job Results**: `extracted_at`, `job_result_id`, `results`

This configuration ensures a robust and flexible integration with the third-party analytics API, allowing for efficient data extraction and management.