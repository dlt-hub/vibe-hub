# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the extraction and synchronization of survey and response data from the API, supporting offset-based pagination and custom authentication.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/v1/",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["token"]
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
                "path": "/v1/nudges",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [500, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "responses",
            "endpoint": {
                "path": "/v1/nudges/{parent_id}/responses.json",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [500, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: The API key for accessing the service.
  - `token`: The token for authenticating requests.

## Resources

### Surveys

- **Resource Name**: surveys
- **Endpoint Path**: `/v1/nudges`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 500
  - **Offset Parameter**: `offset`
- **Data Selector**: `data`

### Responses

- **Resource Name**: responses
- **Endpoint Path**: `/v1/nudges/{parent_id}/responses.json`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 500
  - **Offset Parameter**: `offset`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 500, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

### Surveys

- **Fields**:
  - `type`: string or null
  - `active`: boolean or null
  - `canonical_name`: string or null
  - `created_at`: string (date-time) or null
  - `id`: integer or null
  - `kind`: string or null
  - `name`: string or null
  - `site_id`: integer or null
  - `survey_url`: string or null
  - `uuid`: string or null

### Responses

- **Fields**:
  - `anon_visitor_id`: string or null
  - `answered_questions`: array of objects or null
  - `emitted_at`: string (date-time) or null
  - `id`: integer or null
  - `identity`: string or null
  - `ip_address`: string or null
  - `location`: string or null
  - `nps`: object or null
    - `category`: string or null
    - `reason`: string or null
    - `respondent_id`: integer or null
    - `response_uri`: string or null
    - `score`: integer or null
    - `time`: string (date-time) or null
  - `nudge_id`: integer or null
  - `nudge_name`: string or null
  - `page`: string or null
  - `properties`: object or null
  - `referrer`: string or null
  - `time`: string (date-time) or null
  - `user_agent`: string or null

This configuration provides a clean and vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format.