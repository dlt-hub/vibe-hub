# Overview

This document provides a configuration example for integrating with a third-party recruitment API using the dltHub REST API source. The integration allows for the extraction of data related to candidates, offers, and departments from the API. The configuration is designed to be vendor-neutral and does not include any proprietary or branded content.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/c/{company_id}",
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
            "name": "candidates",
            "endpoint": {
                "path": "/candidates",
                "data_selector": "candidates"
            }
        },
        {
            "name": "offers",
            "endpoint": {
                "path": "/offers",
                "data_selector": "offers"
            }
        },
        {
            "name": "departments",
            "endpoint": {
                "path": "/departments",
                "data_selector": "departments"
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer Token
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Candidates

- **Resource Name**: candidates
- **Endpoint Path**: `/candidates`
- **Data Selector**: `candidates`
- **Primary Key**: `id`

### Offers

- **Resource Name**: offers
- **Endpoint Path**: `/offers`
- **Data Selector**: `offers`
- **Primary Key**: `id`

### Departments

- **Resource Name**: departments
- **Endpoint Path**: `/departments`
- **Data Selector**: `departments`
- **Primary Key**: `id`

## Error Handling

- **Retry Logic**: Implemented with exponential backoff
- **HTTP Status Codes for Retry**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Candidates

- **Fields**:
  - `admin_id`: integer or null
  - `adminapp_url`: string or null
  - `created_at`: string
  - `emails`: array of strings
  - `example`: boolean
  - `followed`: boolean
  - `has_avatar`: boolean
  - `id`: integer
  - `initials`: string or null
  - `last_message_at`: string or null
  - `my_last_rating`: string or null
  - `my_pending_result_request`: boolean
  - `my_upcoming_event`: boolean
  - `name`: string
  - `notes_count`: integer
  - `pending_result_request`: boolean
  - `phones`: array of strings
  - `photo_thumb_url`: string or null
  - `placements`: array of objects
  - `positive_ratings`: integer or null
  - `rating`: integer
  - `ratings`: object
  - `ratings_count`: integer
  - `referrer`: string or null
  - `source`: string or null
  - `tasks_count`: integer
  - `unread_notifications`: boolean
  - `upcoming_event`: boolean
  - `updated_at`: string
  - `viewed`: boolean

### Offers

- **Fields**:
  - `adminapp_url`: string or null
  - `candidates_count`: integer
  - `careers_url`: string
  - `city`: string or null
  - `closed_at`: string or null
  - `country_code`: string or null
  - `created_at`: string
  - `department`: string or null
  - `department_id`: integer
  - `disqualified_candidates_count`: integer
  - `eeo_settings`: string or null
  - `employment_type`: string or null
  - `enabled_for_referrals`: boolean
  - `enabled_languages`: array of objects
  - `example`: boolean
  - `followed`: boolean
  - `followers`: array
  - `guid`: string
  - `has_active_campaign`: boolean
  - `hired_candidates_count`: integer
  - `hiring_manager_id`: integer or null
  - `id`: integer
  - `job_scheduler`: string or null
  - `kind`: string
  - `location`: string
  - `mailbox_email`: string or null
  - `number_of_openings`: integer or null
  - `offer_tags`: array
  - `pipeline`: boolean
  - `pipeline_template`: object or null
  - `position`: integer or null
  - `postal_code`: string or null
  - `published_at`: string or null
  - `qualified_candidates_count`: integer
  - `recruiter_id`: integer or null
  - `slug`: string
  - `state_code`: string or null
  - `state_name`: string or null
  - `status`: string
  - `street`: string or null
  - `title`: string
  - `updated_at`: string
  - `url`: string

### Departments

- **Fields**:
  - `grouped_translations`: object
  - `id`: integer
  - `name`: string
  - `offers_count`: integer
  - `status`: string or null

This configuration provides a comprehensive setup for accessing and extracting data from a recruitment API, ensuring a clean and vendor-neutral integration.