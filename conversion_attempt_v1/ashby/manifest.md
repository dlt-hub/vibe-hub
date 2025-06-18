# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and schema. The API uses basic authentication and supports cursor-based pagination for data retrieval.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "applications",
            "endpoint": {
                "path": "/v1/applications",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "archive_reasons",
            "endpoint": {
                "path": "/v1/archive_reasons",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "candidate_tags",
            "endpoint": {
                "path": "/v1/candidate_tags",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "candidates",
            "endpoint": {
                "path": "/v1/candidates",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/v1/custom_fields",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "departments",
            "endpoint": {
                "path": "/v1/departments",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "feedback_form_definitions",
            "endpoint": {
                "path": "/v1/feedback_form_definitions",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "interview_schedules",
            "endpoint": {
                "path": "/v1/interview_schedules",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "job_postings",
            "endpoint": {
                "path": "/v1/job_postings",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "jobs",
            "endpoint": {
                "path": "/v1/jobs",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v1/locations",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "offers",
            "endpoint": {
                "path": "/v1/offers",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "sources",
            "endpoint": {
                "path": "/v1/sources",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "method": "POST",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "response.nextCursor"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: Used as both username and password for authentication.

# Resources

Each resource corresponds to a specific endpoint and supports cursor-based pagination. The data is extracted from the `results` field in the response.

- **Resource Name**: applications
  - **Endpoint Path**: `/v1/applications`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: archive_reasons
  - **Endpoint Path**: `/v1/archive_reasons`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: candidate_tags
  - **Endpoint Path**: `/v1/candidate_tags`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: candidates
  - **Endpoint Path**: `/v1/candidates`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: custom_fields
  - **Endpoint Path**: `/v1/custom_fields`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: departments
  - **Endpoint Path**: `/v1/departments`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: feedback_form_definitions
  - **Endpoint Path**: `/v1/feedback_form_definitions`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: interview_schedules
  - **Endpoint Path**: `/v1/interview_schedules`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: job_postings
  - **Endpoint Path**: `/v1/job_postings`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: jobs
  - **Endpoint Path**: `/v1/jobs`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: locations
  - **Endpoint Path**: `/v1/locations`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: offers
  - **Endpoint Path**: `/v1/offers`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: sources
  - **Endpoint Path**: `/v1/sources`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

- **Resource Name**: users
  - **Endpoint Path**: `/v1/users`
  - **HTTP Method**: POST
  - **Data Selector**: `results`
  - **Pagination**: Cursor-based with `cursor` as the cursor parameter and `response.nextCursor` as the cursor path.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a defined schema with fields, types, and nullability. The field names are neutral and generalized for public use.