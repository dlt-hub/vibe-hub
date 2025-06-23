# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources such as applications, datasets, models, and more, with pagination and error handling capabilities.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
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
                "path": "/v2/users/{user_id}/apps",
                "data_selector": "apps",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "datasets",
            "endpoint": {
                "path": "/v2/users/{user_id}/apps/{app_id}/datasets",
                "data_selector": "datasets",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "models",
            "endpoint": {
                "path": "/v2/users/{user_id}/apps/{app_id}/models",
                "data_selector": "models",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "model_versions",
            "endpoint": {
                "path": "/v2/users/{user_id}/apps/{app_id}/models/{model_id}/versions",
                "data_selector": "model_versions",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "workflows",
            "endpoint": {
                "path": "/v2/users/{user_id}/apps/{app_id}/workflows",
                "data_selector": "workflows",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "app_inputs",
            "endpoint": {
                "path": "/v2/users/{user_id}/apps/{app_id}/inputs",
                "data_selector": "inputs",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "app_inputs_jobs",
            "endpoint": {
                "path": "/v2/users/{user_id}/apps/{app_id}/inputs/jobs/add",
                "data_selector": "inputs_add_jobs",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "app_concepts",
            "endpoint": {
                "path": "/v2/users/{user_id}/apps/{app_id}/concepts",
                "data_selector": "concepts",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
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

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Applications
- **Endpoint Path**: `/v2/users/{user_id}/apps`
- **Data Selector**: `apps`
- **Pagination**: Page number with `page` and `per_page` parameters, limit 20

### Datasets
- **Endpoint Path**: `/v2/users/{user_id}/apps/{app_id}/datasets`
- **Data Selector**: `datasets`
- **Pagination**: Page number with `page` and `per_page` parameters, limit 100

### Models
- **Endpoint Path**: `/v2/users/{user_id}/apps/{app_id}/models`
- **Data Selector**: `models`
- **Pagination**: Page number with `page` and `per_page` parameters, limit 100

### Model Versions
- **Endpoint Path**: `/v2/users/{user_id}/apps/{app_id}/models/{model_id}/versions`
- **Data Selector**: `model_versions`
- **Pagination**: Page number with `page` and `per_page` parameters, limit 100

### Workflows
- **Endpoint Path**: `/v2/users/{user_id}/apps/{app_id}/workflows`
- **Data Selector**: `workflows`
- **Pagination**: Page number with `page` and `per_page` parameters, limit 100

### App Inputs
- **Endpoint Path**: `/v2/users/{user_id}/apps/{app_id}/inputs`
- **Data Selector**: `inputs`
- **Pagination**: Page number with `page` and `per_page` parameters, limit 20

### App Inputs Jobs
- **Endpoint Path**: `/v2/users/{user_id}/apps/{app_id}/inputs/jobs/add`
- **Data Selector**: `inputs_add_jobs`
- **Pagination**: Page number with `page` and `per_page` parameters, limit 20

### App Concepts
- **Endpoint Path**: `/v2/users/{user_id}/apps/{app_id}/concepts`
- **Data Selector**: `concepts`
- **Pagination**: Page number with `page` and `per_page` parameters, limit 20

## Error Handling

- **Retry on Status Codes**: 429
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a schema with fields such as `id`, `modified_at`, and other relevant properties. The schemas are designed to be flexible and accommodate various data types, ensuring compatibility with the API's responses.