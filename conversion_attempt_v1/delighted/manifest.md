# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, including people, unsubscribes, bounces, and survey responses. The configuration is designed to be vendor-neutral and follows dltHub's REST API source format.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["nothing"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "people",
            "endpoint": {
                "path": "/v1/people.json",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "next not in headers['link']"
                }
            }
        },
        {
            "name": "unsubscribes",
            "endpoint": {
                "path": "/v1/unsubscribes.json",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "bounces",
            "endpoint": {
                "path": "/v1/bounces.json",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "survey_responses",
            "endpoint": {
                "path": "/v1/survey_responses.json",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
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
  - `nothing`: Placeholder for password, not used in this context.

## Resources

### People
- **Endpoint Path**: `/v1/people.json`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`
- **Primary Key**: `id`

### Unsubscribes
- **Endpoint Path**: `/v1/unsubscribes.json`
- **Pagination Strategy**: Page number-based
- **Data Selector**: `data`
- **Primary Key**: `person_id`

### Bounces
- **Endpoint Path**: `/v1/bounces.json`
- **Pagination Strategy**: Page number-based
- **Data Selector**: `data`
- **Primary Key**: `person_id`

### Survey Responses
- **Endpoint Path**: `/v1/survey_responses.json`
- **Pagination Strategy**: Page number-based
- **Data Selector**: `data`
- **Primary Key**: `id`

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2
- **Timeout Handling**: Generalized to handle timeouts and data limits.

## Schema

### People
- **Fields**:
  - `created_at`: Integer, timestamp of creation
  - `email`: String, email address
  - `id`: String, unique identifier
  - `last_responded_at`: Integer, timestamp of last response
  - `last_sent_at`: Integer, timestamp of last sent survey
  - `name`: String, full name
  - `next_survey_scheduled_at`: Integer, timestamp of next scheduled survey
  - `phone_number`: String, phone number

### Unsubscribes
- **Fields**:
  - `email`: String, email address
  - `name`: String, name of the subscriber
  - `person_id`: String, unique identifier
  - `unsubscribed_at`: Integer, timestamp of unsubscription

### Bounces
- **Fields**:
  - `bounced_at`: Integer, timestamp of bounce
  - `email`: String, email address
  - `name`: String, name associated with the email
  - `person_id`: String, unique identifier

### Survey Responses
- **Fields**:
  - `additional_answers`: Array, additional answers in the survey
  - `comment`: String, additional comments
  - `created_at`: Integer, timestamp of creation
  - `id`: String, unique identifier
  - `notes`: Array, additional notes
  - `permalink`: String, permanent link
  - `person`: String, information about the respondent
  - `person_properties`: Object, additional properties
  - `score`: Integer, survey score
  - `survey_type`: String, type of survey
  - `tags`: Array, associated tags
  - `updated_at`: Integer, timestamp of last update

This configuration provides a comprehensive setup for integrating with a third-party analytics API using dltHub's REST API source, ensuring a clean and vendor-neutral approach.