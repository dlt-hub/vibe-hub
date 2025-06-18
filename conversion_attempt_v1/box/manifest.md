# Overview

This document provides a configuration example for integrating with a third-party cloud storage API using dltHub's REST API source. The integration allows users to list, access, and synchronize various resources such as events, templates, collections, and more from their cloud storage. This setup helps automate workflows by integrating cloud storage data with other tools, ensuring efficient file management and analysis.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/oauth2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "entries",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "stream_position",
                    "cursor_value": "{{ response.get('next_stream_position') }}",
                    "stop_condition": "{{ response.chunk_size == 0 }}"
                }
            }
        },
        {
            "name": "sign_templates",
            "endpoint": {
                "path": "/v1/sign_templates",
                "data_selector": "entries",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "marker",
                    "cursor_value": "{{ response.get('next_marker') }}",
                    "stop_condition": "{{ response.get('next_marker') is none }}"
                }
            }
        },
        {
            "name": "collections",
            "endpoint": {
                "path": "/v1/collections",
                "data_selector": "entries",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "collection_items",
            "endpoint": {
                "path": "/v1/collections/{{ stream_partition.collection }}/items",
                "data_selector": "entries",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "sign_request",
            "endpoint": {
                "path": "/v1/sign_requests",
                "data_selector": "entries",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "marker",
                    "cursor_value": "{{ response.get('next_marker') }}",
                    "stop_condition": "{{ response.get('next_marker') is none }}"
                }
            }
        },
        {
            "name": "admin_logs",
            "endpoint": {
                "path": "/v1/events?stream_type=admin_logs",
                "data_selector": "entries",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "stream_position",
                    "cursor_value": "{{ response.get('next_stream_position', '') }}",
                    "stop_condition": "{{ response.chunk_size == 0 }}"
                }
            }
        },
        {
            "name": "files",
            "endpoint": {
                "path": "/v1/folders/0/items",
                "data_selector": "entries",
                "record_filter": "{{ record['type'] == 'file' }}",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "file_collaborations",
            "endpoint": {
                "path": "/v1/files/{{ stream_slice['file_id'] }}/collaborations",
                "data_selector": "entries",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "marker",
                    "cursor_value": "{{ response.get('next_marker') }}",
                    "stop_condition": "{{ response.get('next_marker') is none }}"
                }
            }
        },
        {
            "name": "folder_collaborations",
            "endpoint": {
                "path": "/v1/folders/{{ stream_slice['folder_id'] }}/collaborations",
                "data_selector": "entries",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "marker",
                    "cursor_value": "{{ response.get('next_marker') }}",
                    "stop_condition": "{{ response.get('next_marker') is none }}"
                }
            }
        },
        {
            "name": "file_comments",
            "endpoint": {
                "path": "/v1/files/{{ stream_slice['file_id'] }}/comments",
                "data_selector": "entries",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "recent_items",
            "endpoint": {
                "path": "/v1/recent_items",
                "data_selector": "entries.*.item",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "marker",
                    "cursor_value": "{{ response.get('next_marker') }}",
                    "stop_condition": "{{ response.get('next_marker') is none }}"
                }
            }
        },
        {
            "name": "file_tasks",
            "endpoint": {
                "path": "/v1/files/{{ stream_slice['file_id'] }}/tasks",
                "data_selector": "entries.*.task_assignment_collection.entries.*"
            }
        },
        {
            "name": "trashed_items",
            "endpoint": {
                "path": "/v1/folders/trash/items",
                "data_selector": "entries",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "entries",
                "paginator": {
                    "type": "offset",
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "folders",
            "endpoint": {
                "path": "/v1/folders/0/items",
                "data_selector": "entries",
                "record_filter": "{{ record['type'] == 'folder' }}",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`
  - `client_secret`
- **Token URL**: `https://api.example.com/oauth2/token`

# Resources

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Generalized paths such as `/v1/resource`
- **Pagination Strategy**: 
  - Cursor-based with parameters like `stream_position` or `marker`
  - Offset-based with parameters like `offset`
- **Data Selector**: Typically `entries` or nested paths

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500)
- **Backoff Strategy**: Exponential backoff with a configurable factor
- **Timeouts**: Generalized handling for request timeouts

# Schema

- **Fields**: Use neutral field names such as `id`, `name`, `type`
- **Types**: Consistent with the YAML schema definitions, using types like `string`, `number`, `boolean`
- **Nullability**: Fields can be nullable as indicated in the schema

This configuration provides a clean, vendor-neutral setup for integrating with a third-party cloud storage API using dltHub's REST API source. It abstracts away proprietary details while maintaining the necessary structure and parameters for effective data synchronization.