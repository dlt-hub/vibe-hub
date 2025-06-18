# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various resources such as contacts, companies, deals, notes, tasks, events, milestones, campaigns, documents, ticket filters, and tickets. Each resource is accessed via specific endpoints and supports pagination and authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["email"],
            "password": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "not last_record.get('cursor')"
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
                    "cursor_param": "cursor"
                }
            }
        },
        {
            "name": "deals",
            "endpoint": {
                "path": "/v1/deals",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor"
                }
            }
        },
        {
            "name": "notes",
            "endpoint": {
                "path": "/v1/notes",
                "data_selector": "data"
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/v1/tasks",
                "data_selector": "data"
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "data"
            }
        },
        {
            "name": "milestone",
            "endpoint": {
                "path": "/v1/milestone",
                "data_selector": "data"
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor"
                }
            }
        },
        {
            "name": "documents",
            "endpoint": {
                "path": "/v1/documents",
                "data_selector": "data"
            }
        },
        {
            "name": "ticket_filters",
            "endpoint": {
                "path": "/v1/ticket_filters",
                "data_selector": "data"
            }
        },
        {
            "name": "tickets",
            "endpoint": {
                "path": "/v1/tickets",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `email`: The email address used as the username for authentication.
  - `api_key`: The API key used as the password for authentication.

# Resources

Each resource is defined with a name, endpoint path, and pagination strategy. The data selector is used to extract the relevant data from the API response.

- **Contacts**: `/v1/contacts`, cursor pagination
- **Companies**: `/v1/companies`, cursor pagination
- **Deals**: `/v1/deals`, cursor pagination
- **Notes**: `/v1/notes`, single page
- **Tasks**: `/v1/tasks`, single page
- **Events**: `/v1/events`, single page
- **Milestone**: `/v1/milestone`, single page
- **Campaigns**: `/v1/campaigns`, cursor pagination
- **Documents**: `/v1/documents`, single page
- **Ticket Filters**: `/v1/ticket_filters`, single page
- **Tickets**: `/v1/tickets`, single page

# Error Handling

The configuration includes retry logic for handling HTTP errors. The system will retry requests on specific status codes with a backoff strategy.

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to ensure neutrality.

- **Contacts**: Includes fields like `id`, `created_time`, `updated_time`, etc.
- **Companies**: Includes fields like `id`, `created_time`, `updated_time`, etc.
- **Deals**: Includes fields like `id`, `name`, `created_time`, etc.
- **Notes**: Includes fields like `id`, `description`, `created_time`, etc.
- **Tasks**: Includes fields like `id`, `subject`, `created_time`, etc.
- **Events**: General schema with additional properties.
- **Milestone**: Includes fields like `id`, `name`, `milestones`, etc.
- **Campaigns**: Includes fields like `id`, `name`, `created_time`, etc.
- **Documents**: Includes fields like `id`, `name`, `uploaded_time`, etc.
- **Ticket Filters**: Includes fields like `id`, `name`, `conditions`, etc.
- **Tickets**: Includes fields like `id`, `subject`, `created_time`, etc.