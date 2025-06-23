# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key.

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
            "name": "members",
            "endpoint": {
                "path": "/v1/members",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "{{ not response.get('next', {}) }}"
                }
            }
        },
        {
            "name": "organization_invitations",
            "endpoint": {
                "path": "/v1/organization-invitations",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "{{ not response.get('next', {}) }}"
                }
            }
        },
        {
            "name": "member_fields",
            "endpoint": {
                "path": "/v1/member-fields",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "{{ not response.get('next', {}) }}"
                }
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/v1/activities",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "{{ 'next' not in response or response['next'] == none or response['next'] == 'inf' }}"
                }
            }
        },
        {
            "name": "notes",
            "endpoint": {
                "path": "/v1/notes/members",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "{{ not response.get('next', {}) }}"
                }
            }
        },
        {
            "name": "actions",
            "endpoint": {
                "path": "/v1/actions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "{{ not response.get('next', {}) }}"
                }
            }
        },
        {
            "name": "candidates",
            "endpoint": {
                "path": "/v1/candidates",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "{{ not response.get('next', {}) }}"
                }
            }
        },
        {
            "name": "member_deactivated_actions",
            "endpoint": {
                "path": "/v1/actions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "{{ not response.get('next', {}) }}"
                }
            }
        },
        {
            "name": "candidate_profile_viewed_actions",
            "endpoint": {
                "path": "/v1/actions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "{{ not response.get('next', {}) }}"
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

### Members
- **Endpoint Path**: `/v1/members`
- **Pagination**: Cursor-based with `next` as the cursor parameter
- **Data Selector**: `data`

### Organization Invitations
- **Endpoint Path**: `/v1/organization-invitations`
- **Pagination**: Cursor-based with `next` as the cursor parameter
- **Data Selector**: `data`

### Member Fields
- **Endpoint Path**: `/v1/member-fields`
- **Pagination**: Cursor-based with `next` as the cursor parameter
- **Data Selector**: `data`

### Activities
- **Endpoint Path**: `/v1/activities`
- **Pagination**: Cursor-based with `next` as the cursor parameter
- **Data Selector**: `data`

### Notes
- **Endpoint Path**: `/v1/notes/members`
- **Pagination**: Cursor-based with `next` as the cursor parameter
- **Data Selector**: `data`

### Actions
- **Endpoint Path**: `/v1/actions`
- **Pagination**: Cursor-based with `next` as the cursor parameter
- **Data Selector**: `data`

### Candidates
- **Endpoint Path**: `/v1/candidates`
- **Pagination**: Cursor-based with `next` as the cursor parameter
- **Data Selector**: `data`

### Member Deactivated Actions
- **Endpoint Path**: `/v1/actions`
- **Pagination**: Cursor-based with `next` as the cursor parameter
- **Data Selector**: `data`

### Candidate Profile Viewed Actions
- **Endpoint Path**: `/v1/actions`
- **Pagination**: Cursor-based with `next` as the cursor parameter
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implemented with a constant backoff strategy of 5 seconds
- **HTTP Status Codes**: Retry on 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

- **Fields**: Use neutral field names such as `customer_id` instead of branded terms.
- **Types**: Consistent with the YAML schema definitions, ensuring compatibility with dltHub's data handling.