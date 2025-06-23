# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and configuration settings. The API uses OAuth2.0 for authentication and supports incremental data synchronization based on timestamps.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["access_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "activity_logs",
            "endpoint": {
                "path": "/v1/admins/activity_logs",
                "data_selector": "activity_logs",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "not response.get('pages', {}).get('next')"
                }
            }
        },
        {
            "name": "admins",
            "endpoint": {
                "path": "/v1/admins",
                "data_selector": "admins"
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/tags",
                "data_selector": "data"
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/v1/teams",
                "data_selector": "teams"
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/v1/segments",
                "data_selector": "segments",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "not response.get('pages', {}).get('next')"
                }
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/v1/companies/scroll",
                "data_selector": "data",
                "paginator": {
                    "type": "custom",
                    "cursor_param": "scroll_param"
                }
            }
        },
        {
            "name": "company_attributes",
            "endpoint": {
                "path": "/v1/data_attributes",
                "data_selector": "data",
                "params": {
                    "model": "company"
                }
            }
        },
        {
            "name": "contact_attributes",
            "endpoint": {
                "path": "/v1/data_attributes",
                "data_selector": "data",
                "params": {
                    "model": "contact"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts/search",
                "data_selector": "data",
                "method": "POST",
                "body": {
                    "query": {
                        "operator": "OR",
                        "value": [
                            {"field": "updated_at", "operator": ">", "value": "{{ stream_interval.get('start_time', {}) or format_datetime(config['start_date'], '%s') }}"},
                            {"field": "updated_at", "operator": "=", "value": "{{ stream_interval.get('start_time', {}) or format_datetime(config['start_date'], '%s') }}"}
                        ]
                    },
                    "sort": {"field": "updated_at", "order": "ascending"}
                },
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "starting_after",
                    "page_size": 150
                }
            }
        },
        {
            "name": "conversations",
            "endpoint": {
                "path": "/v1/conversations/search",
                "data_selector": "conversations",
                "method": "POST",
                "body": {
                    "query": {
                        "operator": "OR",
                        "value": [
                            {"field": "updated_at", "operator": ">", "value": "{{ stream_interval.get('start_time', {}) or format_datetime(config['start_date'], '%s') }}"},
                            {"field": "updated_at", "operator": "=", "value": "{{ stream_interval.get('start_time', {}) or format_datetime(config['start_date'], '%s') }}"}
                        ]
                    },
                    "sort": {"field": "updated_at", "order": "ascending"}
                },
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "starting_after",
                    "page_size": 150
                }
            }
        },
        {
            "name": "conversation_parts",
            "endpoint": {
                "path": "/v1/conversations/{{ stream_slice.id }}",
                "data_selector": "conversation_parts"
            }
        },
        {
            "name": "company_segments",
            "endpoint": {
                "path": "/v1/companies/{{ stream_slice.id }}/segments",
                "data_selector": "data"
            }
        },
        {
            "name": "tickets",
            "endpoint": {
                "path": "/v1/tickets/search",
                "data_selector": "tickets",
                "method": "POST",
                "body": {
                    "query": {
                        "operator": "AND",
                        "value": [
                            {"operator": "OR", "value": [
                                {"field": "updated_at", "operator": ">", "value": "{{ stream_interval['start_time'] }}"},
                                {"field": "updated_at", "operator": "=", "value": "{{ stream_interval['start_time'] }}"}
                            ]},
                            {"field": "updated_at", "operator": "<", "value": "{{ stream_interval['end_time'] }}"}
                        ]
                    },
                    "sort": {"field": "updated_at", "order": "ascending"}
                },
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "starting_after",
                    "page_size": 150
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Access token stored in `dlt.secrets["access_token"]`

# Resources

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: Generalized paths for each resource
- **HTTP Method**: GET or POST as specified
- **Data Selector**: Field path for extracting data
- **Pagination Strategy**: Cursor-based pagination with specified parameters

# Error Handling

- **Retry Logic**: Configured to retry on specific HTTP status codes
- **Backoff Strategy**: Exponential backoff with a factor of 2
- **Known HTTP Response Codes**: 401 for permission errors, 404 for not found, 400 for bad requests

# Schema

- **Fields**: Generalized field names and types
- **Types**: Consistent with the YAML schema, using neutral field names

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. It abstracts away any proprietary details and focuses on the reusable patterns for open-source publication.