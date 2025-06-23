# Overview

This document provides a configuration guide for integrating with a third-party event management API using the dltHub REST API source. The API allows access to various resources such as organizations, events, attendees, and more, enabling users to retrieve and manage data related to event management.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["private_token"]
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
                "path": "/v3/users/me/organizations/",
                "data_selector": "organizations",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v3/organizations/{organization_id}/events/",
                "data_selector": "events",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        },
        {
            "name": "attendees",
            "endpoint": {
                "path": "/v3/events/{event_id}/attendees/",
                "data_selector": "attendees",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/v3/categories/",
                "data_selector": "categories",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        },
        {
            "name": "formats",
            "endpoint": {
                "path": "/v3/formats/",
                "data_selector": "formats",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/v3/organizations/{organization_id}/orders",
                "data_selector": "orders",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        },
        {
            "name": "organizations_members",
            "endpoint": {
                "path": "/v3/organizations/{organization_id}/members/",
                "data_selector": "members",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        },
        {
            "name": "organizations_roles",
            "endpoint": {
                "path": "/v3/organizations/{organization_id}/roles/",
                "data_selector": "roles",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        },
        {
            "name": "default_questions",
            "endpoint": {
                "path": "/v3/events/{event_id}/canned_questions/",
                "data_selector": "questions",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        },
        {
            "name": "custom_questions",
            "endpoint": {
                "path": "/v3/events/{event_id}/questions/",
                "data_selector": "questions",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        },
        {
            "name": "ticket_buyer_settings",
            "endpoint": {
                "path": "/v3/events/{event_id}/ticket_buyer_settings/",
                "data_selector": []
            }
        },
        {
            "name": "ticket_classes",
            "endpoint": {
                "path": "/v3/events/{event_id}/ticket_classes/",
                "data_selector": "ticket_classes",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        },
        {
            "name": "available_ticket_classes",
            "endpoint": {
                "path": "/v3/events/{event_id}/ticket_classes/for_sale",
                "data_selector": "ticket_classes",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        },
        {
            "name": "venues",
            "endpoint": {
                "path": "/v3/organizations/{organization_id}/venues/",
                "data_selector": "venues",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "continuation",
                    "cursor_path": "pagination.continuation"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `private_token` stored in `dlt.secrets`.

# Resources

- **Organizations**: Access organization data.
- **Events**: Retrieve event details for specific organizations.
- **Attendees**: Fetch attendee information for events.
- **Categories**: List available event categories.
- **Formats**: Retrieve event format details.
- **Orders**: Access order information for organizations.
- **Organizations Members**: Get member details for organizations.
- **Organizations Roles**: Retrieve role information for organizations.
- **Default Questions**: Access default questions for events.
- **Custom Questions**: Retrieve custom questions for events.
- **Ticket Buyer Settings**: Access ticket buyer settings for events.
- **Ticket Classes**: Retrieve ticket class information for events.
- **Available Ticket Classes**: List available ticket classes for sale.
- **Venues**: Access venue details for organizations.

# Error Handling

- **Retry Logic**: Implement retry on status codes 429, 500, 502, 503, 504 with a maximum of 3 retries and a backoff factor of 2.

# Schema

- **Organizations**: Includes fields like `id`, `created`, `name`, etc.
- **Events**: Includes fields like `id`, `changed`, `name`, `start`, `end`, etc.
- **Attendees**: Includes fields like `id`, `changed`, `profile`, etc.
- **Categories**: Includes fields like `id`, `name`, etc.
- **Formats**: Includes fields like `id`, `name`, etc.
- **Orders**: Includes fields like `id`, `changed`, `email`, etc.
- **Organizations Members**: Includes fields like `user_id`, `role_summary`, etc.
- **Organizations Roles**: Includes fields like `id`, `name`, `permissions`, etc.
- **Default Questions**: Includes fields like `id`, `event_id`, `question`, etc.
- **Custom Questions**: Includes fields like `id`, `question`, etc.
- **Ticket Buyer Settings**: Includes fields like `event_id`, `allow_attendee_update`, etc.
- **Ticket Classes**: Includes fields like `id`, `name`, `capacity`, etc.
- **Available Ticket Classes**: Includes fields like `id`, `name`, `cost`, etc.
- **Venues**: Includes fields like `id`, `name`, `address`, etc.