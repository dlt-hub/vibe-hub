# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration facilitates the extraction of interactive content data, such as forms, quizzes, and polls, along with capturing responses and specific questions associated with each item. This setup is ideal for users looking to analyze audience engagement, response patterns, and question insights in their data workflows.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "items",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 50
                }
            }
        },
        {
            "name": "responses",
            "endpoint": {
                "path": "/v1/resource/{{ stream_partition.item_id }}/responses",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 50
                }
            }
        },
        {
            "name": "questions",
            "endpoint": {
                "path": "/v1/resource/{{ stream_partition.item_id }}/questions",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 50
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: Use `api_key` as the username for authentication.

# Resources

## Items
- **Endpoint Path**: `/v1/resource`
- **Pagination Strategy**: Page number pagination with parameters `page[number]` and `page[size]`.
- **Data Selector**: `data`

## Responses
- **Endpoint Path**: `/v1/resource/{{ stream_partition.item_id }}/responses`
- **Pagination Strategy**: Page number pagination with parameters `page[number]` and `page[size]`.
- **Data Selector**: `data`

## Questions
- **Endpoint Path**: `/v1/resource/{{ stream_partition.item_id }}/questions`
- **Pagination Strategy**: Page number pagination with parameters `page[number]` and `page[size]`.
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

## Items
- **Fields**: 
  - `id`: string
  - `type`: string or null
  - `attributes`: object or null
  - `links`: object or null
  - `relationships`: object or null

## Responses
- **Fields**: 
  - `id`: string
  - `type`: string or null
  - `attributes`: object or null
  - `links`: object or null

## Questions
- **Fields**: 
  - `id`: string
  - `type`: string or null
  - `attributes`: object or null

This configuration ensures a clean and efficient integration with the third-party analytics API, allowing for seamless data extraction and analysis.