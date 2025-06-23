# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various resources related to contacts, email campaigns, form submissions, and automations. The API offers both full refresh and incremental sync capabilities, with different rate limits based on the authentication method used.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",  # or "oauth2"
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "forms",
            "endpoint": {
                "path": "/v1/forms",
                "data_selector": "forms",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "after",
                    "cursor_path": "pagination.end_cursor",
                    "stop_condition": "not pagination.has_next_page"
                }
            }
        },
        {
            "name": "sequences",
            "endpoint": {
                "path": "/v1/sequences",
                "data_selector": "sequences",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "after",
                    "cursor_path": "pagination.end_cursor",
                    "stop_condition": "not pagination.has_next_page"
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/tags",
                "data_selector": "tags",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "after",
                    "cursor_path": "pagination.end_cursor",
                    "stop_condition": "not pagination.has_next_page"
                }
            }
        },
        {
            "name": "subscribers",
            "endpoint": {
                "path": "/v1/subscribers",
                "data_selector": "subscribers",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "after",
                    "cursor_path": "pagination.end_cursor",
                    "stop_condition": "not pagination.has_next_page"
                }
            }
        },
        {
            "name": "broadcasts",
            "endpoint": {
                "path": "/v1/broadcasts",
                "data_selector": "broadcasts",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "after",
                    "cursor_path": "pagination.end_cursor",
                    "stop_condition": "not pagination.has_next_page"
                }
            }
        },
        {
            "name": "form_subscribers",
            "endpoint": {
                "path": "/v1/forms/{form_id}/subscribers",
                "data_selector": "subscribers",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "after",
                    "cursor_path": "pagination.end_cursor",
                    "stop_condition": "not pagination.has_next_page"
                }
            }
        },
        {
            "name": "sequence_subscribers",
            "endpoint": {
                "path": "/v1/sequences/{sequence_id}/subscribers",
                "data_selector": "subscribers",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "after",
                    "cursor_path": "pagination.end_cursor",
                    "stop_condition": "not pagination.has_next_page"
                }
            }
        },
        {
            "name": "tag_subscribers",
            "endpoint": {
                "path": "/v1/tags/{tag_id}/subscribers",
                "data_selector": "subscribers",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "after",
                    "cursor_path": "pagination.end_cursor",
                    "stop_condition": "not pagination.has_next_page"
                }
            }
        },
        {
            "name": "current_account",
            "endpoint": {
                "path": "/v1/account",
                "data_selector": "account"
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/v1/custom_fields",
                "data_selector": "custom_fields",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "after",
                    "cursor_path": "pagination.end_cursor",
                    "stop_condition": "not pagination.has_next_page"
                }
            }
        },
        {
            "name": "purchases",
            "endpoint": {
                "path": "/v1/purchases",
                "data_selector": "purchases",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "after",
                    "cursor_path": "pagination.end_cursor",
                    "stop_condition": "not pagination.has_next_page"
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/v1/segments",
                "data_selector": "segments",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "after",
                    "cursor_path": "links.next",
                    "stop_condition": "not links.next"
                }
            }
        },
        {
            "name": "email_templates",
            "endpoint": {
                "path": "/v1/email_templates",
                "data_selector": "email_templates",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "after",
                    "cursor_path": "links.next",
                    "stop_condition": "not links.next"
                }
            }
        }
    ]
})
```

## Authentication

The API supports two types of authentication:

- **API Key**: Used for personal accounts with lower rate limits.
- **OAuth2**: Requires an application setup and provides higher rate limits.

### API Key Authentication

```python
"auth": {
    "type": "api_key",
    "name": "api_key",
    "api_key": dlt.secrets["api_key"],
    "location": "header"
}
```

### OAuth2 Authentication

```python
"auth": {
    "type": "oauth2",
    "client_id": dlt.secrets["client_id"],
    "client_secret": dlt.secrets["client_secret"],
    "token_url": "https://api.example.com/oauth/token"
}
```

## Resources

- **Forms**: `/v1/forms`
- **Sequences**: `/v1/sequences`
- **Tags**: `/v1/tags`
- **Subscribers**: `/v1/subscribers`
- **Broadcasts**: `/v1/broadcasts`
- **Form Subscribers**: `/v1/forms/{form_id}/subscribers`
- **Sequence Subscribers**: `/v1/sequences/{sequence_id}/subscribers`
- **Tag Subscribers**: `/v1/tags/{tag_id}/subscribers`
- **Current Account**: `/v1/account`
- **Custom Fields**: `/v1/custom_fields`
- **Purchases**: `/v1/purchases`
- **Segments**: `/v1/segments`
- **Email Templates**: `/v1/email_templates`

## Error Handling

The API implements retry logic for handling rate limits and server errors:

```python
"endpoint": {
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

## Schema

Each resource has a defined schema with fields such as `id`, `created_at`, and other relevant attributes. The schemas are designed to be flexible and accommodate various data types, ensuring compatibility with the API's responses.