# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, and supports incremental synchronization based on a datetime cursor.

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
            "name": "replicas",
            "endpoint": {
                "path": "/v2/replicas",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "videos",
            "endpoint": {
                "path": "/v2/videos",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50,
                    "start_from_page": 0
                }
            }
        },
        {
            "name": "conversations",
            "endpoint": {
                "path": "/v2/conversations",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "personas",
            "endpoint": {
                "path": "/v2/personas",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "speeches",
            "endpoint": {
                "path": "/v2/speech",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50,
                    "start_from_page": 1
                },
                "error_handler": {
                    "retry_on_status_codes": [500],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: The API key is required for authentication and should be stored securely in dlt's secrets.

# Resources

## Replicas
- **Endpoint Path**: `/v2/replicas`
- **Pagination Strategy**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`

## Videos
- **Endpoint Path**: `/v2/videos`
- **Pagination Strategy**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`

## Conversations
- **Endpoint Path**: `/v2/conversations`
- **Pagination Strategy**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`

## Personas
- **Endpoint Path**: `/v2/personas`
- **Pagination Strategy**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`

## Speeches
- **Endpoint Path**: `/v2/speech`
- **Pagination Strategy**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`
- **Error Handling**: Retries on HTTP 500 status with exponential backoff

# Error Handling

- **Retry/Backoff Logic**: 
  - Retries on HTTP status code 500
  - Maximum of 3 retries with a backoff factor of 2

# Schema

Each resource has its own schema with fields such as `created_at`, `updated_at`, and unique identifiers like `replica_id`, `video_id`, `conversation_id`, `persona_id`, and `speech_id`. These fields are used to ensure data integrity and support incremental synchronization.