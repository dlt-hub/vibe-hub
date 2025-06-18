# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, including surveys, survey questions, survey responses, respondent attributes, and respondent responses. The API uses a cursor-based pagination strategy and requires API key authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2/",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
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
            "name": "surveys",
            "endpoint": {
                "path": "/surveys",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "pagination.next_url",
                    "stop_condition": "not pagination.has_more"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "surveys_questions",
            "endpoint": {
                "path": "/surveys/{survey_id}/questions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "pagination.next_url",
                    "stop_condition": "not pagination.has_more"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "surveys_responses",
            "endpoint": {
                "path": "/surveys/{survey_id}/responses",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "pagination.next_url",
                    "stop_condition": "not pagination.has_more"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "respondents_attributes",
            "endpoint": {
                "path": "/respondents/{respondent_uuid}/attributes",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "pagination.next_url",
                    "stop_condition": "not pagination.has_more"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "respondents_responses",
            "endpoint": {
                "path": "/respondents/{respondent_uuid}/responses",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "pagination.next_url",
                    "stop_condition": "not pagination.has_more"
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
- **Header Name**: Authorization
- **Secret Key**: `api_key` (stored securely in dlt.secrets)

# Resources

### Surveys
- **Endpoint Path**: `/surveys`
- **Primary Key**: `id`
- **Pagination**: Cursor-based using `pagination.next_url`
- **Data Selector**: `data`

### Surveys Questions
- **Endpoint Path**: `/surveys/{survey_id}/questions`
- **Primary Key**: `id`
- **Pagination**: Cursor-based using `pagination.next_url`
- **Data Selector**: `data`

### Surveys Responses
- **Endpoint Path**: `/surveys/{survey_id}/responses`
- **Primary Key**: `uuid`
- **Pagination**: Cursor-based using `pagination.next_url`
- **Data Selector**: `data`

### Respondents Attributes
- **Endpoint Path**: `/respondents/{respondent_uuid}/attributes`
- **Pagination**: Cursor-based using `pagination.next_url`
- **Data Selector**: `data`

### Respondents Responses
- **Endpoint Path**: `/respondents/{respondent_uuid}/responses`
- **Pagination**: Cursor-based using `pagination.next_url`
- **Data Selector**: `data`

# Error Handling

- **Retry on Status Codes**: 429 (Rate Limit)
- **Max Retries**: 3
- **Backoff Strategy**: Exponential with a factor of 2

# Schema

### Surveys
- **Fields**: `id`, `created_at`, `type`, `enabled`, `launch`, `name`, `responses`
- **Primary Key**: `id`

### Surveys Questions
- **Fields**: `id`, `type`, `answer_choices`, `introduction`, `question`
- **Primary Key**: `id`

### Surveys Responses
- **Fields**: `uuid`, `collected_at`, `answers`, `device_type`, `language`, `operating_system`, `respondent`, `url`
- **Primary Key**: `uuid`

### Respondents Attributes
- **Fields**: `id`, `name`, `respondent_uuid`, `value`

### Respondents Responses
- **Fields**: `response`, `survey`
- **Primary Key**: `uuid`