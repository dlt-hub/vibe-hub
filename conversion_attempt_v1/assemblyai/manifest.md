# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, each with specific endpoints and pagination strategies.

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
            "name": "transcripts",
            "endpoint": {
                "path": "/v2/transcript",
                "data_selector": "transcripts",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "page_details.next_url",
                    "stop_condition": "not page_details.next_url"
                }
            }
        },
        {
            "name": "transcript_sentences",
            "endpoint": {
                "path": "/v2/transcript/{tr_id}/sentences",
                "data_selector": "sentences",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "page_details.next_url",
                    "stop_condition": "not page_details.next_url"
                }
            }
        },
        {
            "name": "paragraphs",
            "endpoint": {
                "path": "/v2/transcript/{tr_id}/paragraphs",
                "data_selector": "paragraphs",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "page_details.next_url",
                    "stop_condition": "not page_details.next_url"
                }
            }
        },
        {
            "name": "transcript_subtitle",
            "endpoint": {
                "path": "/v2/transcript/{tr_id}/redacted-audio",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "page_details.next_url",
                    "stop_condition": "not page_details.next_url"
                }
            }
        },
        {
            "name": "lemur_response",
            "endpoint": {
                "path": "/lemur/v3/{request_id}",
                "data_selector": [],
                "error_handler": {
                    "retry_on_status_codes": [401],
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

### Transcripts
- **Endpoint Path**: `/v2/transcript`
- **Data Selector**: `transcripts`
- **Pagination**: Cursor-based using `page_details.next_url`

### Transcript Sentences
- **Endpoint Path**: `/v2/transcript/{tr_id}/sentences`
- **Data Selector**: `sentences`
- **Pagination**: Cursor-based using `page_details.next_url`

### Paragraphs
- **Endpoint Path**: `/v2/transcript/{tr_id}/paragraphs`
- **Data Selector**: `paragraphs`
- **Pagination**: Cursor-based using `page_details.next_url`

### Transcript Subtitle
- **Endpoint Path**: `/v2/transcript/{tr_id}/redacted-audio`
- **Data Selector**: None
- **Pagination**: Cursor-based using `page_details.next_url`

### Lemur Response
- **Endpoint Path**: `/lemur/v3/{request_id}`
- **Data Selector**: None
- **Error Handling**: Retry on HTTP 401 with a maximum of 3 retries and a backoff factor of 2

## Error Handling

- **Retry Logic**: Implemented for specific HTTP status codes (e.g., 401)
- **Backoff Strategy**: Exponential backoff with a factor of 2

## Schema

### Transcripts
- **Fields**: `audio_url`, `completed`, `created`, `error`, `id`, `resource_url`, `status`
- **Primary Key**: `id`

### Transcript Sentences
- **Fields**: `confidence`, `end`, `start`, `text`, `uuid`, `words`
- **Primary Key**: `uuid`

### Paragraphs
- **Fields**: `confidence`, `end`, `start`, `text`, `uuid`, `words`
- **Primary Key**: `uuid`

### Transcript Subtitle
- **Fields**: `redacted_audio_url`, `status`, `uuid`
- **Primary Key**: `uuid`

### Lemur Response
- **Fields**: `request_id`, `response`, `usage`
- **Primary Key**: `request_id`

This configuration provides a structured approach to integrating with a third-party analytics API, ensuring data is synchronized efficiently and reliably.