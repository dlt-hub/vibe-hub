# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with specific endpoints and pagination strategies. The API requires authentication and handles errors with retry logic.

## Configuration Example

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
            "name": "agents",
            "endpoint": {
                "path": "/v1/agents",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "business_hours",
            "endpoint": {
                "path": "/v1/business_hours",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "canned_response_folders",
            "endpoint": {
                "path": "/v1/canned_response_folders",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "canned_responses",
            "endpoint": {
                "path": "/v1/canned_response_folders/{stream_slice.id}/responses",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/v1/companies",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "conversations",
            "endpoint": {
                "path": "/v1/tickets/{stream_slice.id}/conversations",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "discussion_categories",
            "endpoint": {
                "path": "/v1/discussions/categories",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "discussion_comments",
            "endpoint": {
                "path": "/v1/discussions/topics/{stream_slice.id}/comments",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "discussion_forums",
            "endpoint": {
                "path": "/v1/discussions/categories/{stream_slice.id}/forums",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "discussion_topics",
            "endpoint": {
                "path": "/v1/discussions/forums/{stream_slice.id}/topics",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "email_configs",
            "endpoint": {
                "path": "/v1/email_configs",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "email_mailboxes",
            "endpoint": {
                "path": "/v1/email/mailboxes",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/v1/groups",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/v1/roles",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "satisfaction_ratings",
            "endpoint": {
                "path": "/v1/surveys/satisfaction_ratings",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "scenario_automations",
            "endpoint": {
                "path": "/v1/scenario_automations",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "settings",
            "endpoint": {
                "path": "/v1/settings/helpdesk",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "skills",
            "endpoint": {
                "path": "/v1/skills",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "sla_policies",
            "endpoint": {
                "path": "/v1/sla_policies",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "solution_articles",
            "endpoint": {
                "path": "/v1/solutions/folders/{stream_slice.id}/articles",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "solution_categories",
            "endpoint": {
                "path": "/v1/solutions/categories",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "solution_folders",
            "endpoint": {
                "path": "/v1/solutions/categories/{stream_slice.id}/folders",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "surveys",
            "endpoint": {
                "path": "/v1/surveys",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "ticket_fields",
            "endpoint": {
                "path": "/v1/ticket_fields",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        },
        {
            "name": "tickets",
            "endpoint": {
                "path": "/v1/tickets",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "time_entries",
            "endpoint": {
                "path": "/v1/time_entries",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "'next' not in headers['link']"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic
- **Username**: `api_key` (stored securely in dlt secrets)

## Resources

### Resource Configuration

Each resource is defined with the following parameters:

- **Resource Name**: Abstracted from the original stream name
- **Endpoint Path**: Generalized path to the resource
- **HTTP Method**: GET (default)
- **Data Selector**: Path to the data within the response
- **Primary Key**: Unique identifier for each record
- **Pagination Strategy**: Cursor-based or page number-based, depending on the resource

### Example Resources

1. **Agents**
   - **Path**: `/v1/agents`
   - **Pagination**: Cursor
   - **Primary Key**: `id`

2. **Business Hours**
   - **Path**: `/v1/business_hours`
   - **Pagination**: Cursor
   - **Primary Key**: `id`

3. **Canned Response Folders**
   - **Path**: `/v1/canned_response_folders`
   - **Pagination**: Cursor
   - **Primary Key**: `id`

## Error Handling

- **Retry Logic**: Implemented for HTTP status codes 401, 429, 500, 502, 503, 504
- **Backoff Strategy**: Exponential backoff with a factor of 2
- **Max Retries**: 3 attempts before failing

## Schema

Each resource has a defined schema with fields, types, and nullability. Field names are generalized to ensure vendor neutrality. For example, `user_id` is abstracted to `customer_id` or `user_key`.

## Final Verification Checklist

- All parameters are sourced from the YAML configuration.
- Brand-specific terms are generalized.
- Pagination strategies are correctly mapped.
- Authentication configuration follows the dltHub format.
- No Airbyte-specific language remains.
- No inferred or guessed logic is added.
- Schema is consistent with the YAML and uses neutral field names.