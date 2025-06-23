# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the extraction of various resources such as organizations, events, agenda items, discussion groups, tracks, webinars, and event members. The API supports a simple GET request method with API key authentication and does not require pagination.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
            "api_key": dlt.secrets["access_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v1/organizations",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "agenda_items",
            "endpoint": {
                "path": "/v1/agenda-items",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "discussion_groups",
            "endpoint": {
                "path": "/v1/discussion-groups",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "tracks",
            "endpoint": {
                "path": "/v1/tracks",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "webinars",
            "endpoint": {
                "path": "/v1/webinars",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "event_members",
            "endpoint": {
                "path": "/v1/event-members",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Header Name**: `Authorization`
- **API Key**: Stored securely in `dlt.secrets["access_key"]`
- **Location**: `header`

## Resources

### Organizations
- **Endpoint Path**: `/v1/organizations`
- **Primary Key**: `id`
- **Pagination**: `single_page`
- **Data Selector**: `data`

### Events
- **Endpoint Path**: `/v1/events`
- **Primary Key**: `id`
- **Pagination**: `single_page`
- **Data Selector**: `data`

### Agenda Items
- **Endpoint Path**: `/v1/agenda-items`
- **Primary Key**: `id`
- **Pagination**: `single_page`
- **Data Selector**: `data`

### Discussion Groups
- **Endpoint Path**: `/v1/discussion-groups`
- **Primary Key**: `id`
- **Pagination**: `single_page`
- **Data Selector**: `data`

### Tracks
- **Endpoint Path**: `/v1/tracks`
- **Primary Key**: `id`
- **Pagination**: `single_page`
- **Data Selector**: `data`

### Webinars
- **Endpoint Path**: `/v1/webinars`
- **Primary Key**: `id`
- **Pagination**: `single_page`
- **Data Selector**: `data`

### Event Members
- **Endpoint Path**: `/v1/event-members`
- **Primary Key**: `id`
- **Pagination**: `single_page`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Not specified in the YAML, default retry logic can be implemented as needed.
- **HTTP Status Codes**: Handle common HTTP errors such as 429, 500, 502, 503, and 504 with retries.
- **Backoff Strategy**: Implement exponential backoff if required.

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each resource's schema includes fields such as `id`, `created_at`, `updated_at`, and other relevant attributes, with types like `string`, `integer`, `boolean`, and `object`.