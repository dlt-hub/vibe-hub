# Overview

This document provides a configuration guide for integrating with a third-party survey API using the dltHub REST API source. The integration allows for data ingestion from various endpoints, including surveys, questions, answers, and responses. Authentication is handled via an API key, and the API supports pagination for efficient data retrieval.

## Configuration Example

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
            "name": "surveys",
            "endpoint": {
                "path": "/surveys",
                "data_selector": "surveys",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "questions",
            "endpoint": {
                "path": "/questions",
                "data_selector": "questions",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "answers",
            "endpoint": {
                "path": "/answers/search",
                "method": "POST",
                "data_selector": "answers",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "responses",
            "endpoint": {
                "path": "/responses/search",
                "method": "POST",
                "data_selector": "responses",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Configuration**: The API key is injected into the request header for authentication. The key should be stored securely and accessed via `dlt.secrets["api_key"]`.

## Resources

### Surveys
- **Endpoint Path**: `/surveys`
- **HTTP Method**: GET
- **Data Selector**: `surveys`
- **Pagination**: Page number-based with parameters `page` and `page_size`, default limit of 100.

### Questions
- **Endpoint Path**: `/questions`
- **HTTP Method**: GET
- **Data Selector**: `questions`
- **Pagination**: Page number-based with parameters `page` and `page_size`, default limit of 100.

### Answers
- **Endpoint Path**: `/answers/search`
- **HTTP Method**: POST
- **Data Selector**: `answers`
- **Pagination**: Page number-based with parameters `page` and `page_size`, default limit of 100.

### Responses
- **Endpoint Path**: `/responses/search`
- **HTTP Method**: POST
- **Data Selector**: `responses`
- **Pagination**: Page number-based with parameters `page` and `page_size`, default limit of 100.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Surveys
- **Fields**: `id`, `brand_name`, `metric`, `name`, `survey_token`, `survey_type`
- **Primary Key**: `id`

### Questions
- **Fields**: `id`, `choices`, `metric`, `order`, `rating_scale`, `required`, `rules`, `survey`, `text`
- **Primary Key**: `id`

### Answers
- **Fields**: `id`, `choice`, `choice_label`, `created`, `follow_up_answer`, `modified`, `published_as_testimonial`, `question`, `response_id`, `sentiment`, `survey`
- **Primary Key**: `id`

### Responses
- **Fields**: `id`, `answers`, `created`, `customer`, `ip_address`, `modified`, `survey`, `ticket`
- **Primary Key**: `id`

This configuration provides a comprehensive setup for integrating with the survey API, ensuring efficient data retrieval and management through dltHub's REST API source.