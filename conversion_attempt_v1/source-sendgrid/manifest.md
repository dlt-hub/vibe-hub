# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with specific endpoints and pagination strategies. The API requires authentication via an API key, and supports incremental synchronization based on datetime cursors.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
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
            "name": "bounces",
            "endpoint": {
                "path": "/v1/suppression/bounces",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "spam_reports",
            "endpoint": {
                "path": "/v1/suppression/spam_reports",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "global_suppressions",
            "endpoint": {
                "path": "/v1/suppression/unsubscribes",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "blocks",
            "endpoint": {
                "path": "/v1/suppression/blocks",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "invalid_emails",
            "endpoint": {
                "path": "/v1/suppression/invalid_emails",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "suppression_groups",
            "endpoint": {
                "path": "/v1/asm/groups",
                "data_selector": "data"
            }
        },
        {
            "name": "suppression_group_members",
            "endpoint": {
                "path": "/v1/asm/suppressions",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "lists",
            "endpoint": {
                "path": "/v1/marketing/lists",
                "data_selector": "result",
                "paginator": {
                    "type": "cursor",
                    "page_size": 1000,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('_metadata', {}).get('next', {})"
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/v1/marketing/segments/2.0",
                "data_selector": "results"
            }
        },
        {
            "name": "singlesend_stats",
            "endpoint": {
                "path": "/v1/marketing/stats/singlesends",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "page_size": 50,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('_metadata', {}).get('next', {})"
                }
            }
        },
        {
            "name": "stats_automations",
            "endpoint": {
                "path": "/v1/marketing/stats/automations",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "page_size": 50,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('_metadata', {}).get('next', {})"
                }
            }
        },
        {
            "name": "singlesends",
            "endpoint": {
                "path": "/v1/marketing/singlesends",
                "data_selector": "result",
                "paginator": {
                    "type": "cursor",
                    "page_size": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('_metadata', {}).get('next', {})"
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/v1/templates",
                "data_selector": "result",
                "paginator": {
                    "type": "cursor",
                    "page_size": 200,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('_metadata', {}).get('next', {})"
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/marketing/campaigns",
                "data_selector": "result",
                "paginator": {
                    "type": "cursor",
                    "page_size": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('_metadata', {}).get('next', {})"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/marketing/contacts/exports",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "page_size": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('_metadata', {}).get('next', {})"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **API Key**: Stored securely in `dlt.secrets["api_key"]`
- **Location**: Header

## Resources

### Bounces
- **Endpoint Path**: `/v1/suppression/bounces`
- **Pagination**: Offset with `limit` of 500 and `offset` parameter

### Spam Reports
- **Endpoint Path**: `/v1/suppression/spam_reports`
- **Pagination**: Offset with `limit` of 500 and `offset` parameter

### Global Suppressions
- **Endpoint Path**: `/v1/suppression/unsubscribes`
- **Pagination**: Offset with `limit` of 500 and `offset` parameter

### Blocks
- **Endpoint Path**: `/v1/suppression/blocks`
- **Pagination**: Offset with `limit` of 500 and `offset` parameter

### Invalid Emails
- **Endpoint Path**: `/v1/suppression/invalid_emails`
- **Pagination**: Offset with `limit` of 500 and `offset` parameter

### Suppression Groups
- **Endpoint Path**: `/v1/asm/groups`

### Suppression Group Members
- **Endpoint Path**: `/v1/asm/suppressions`
- **Pagination**: Offset with `limit` of 500 and `offset` parameter

### Lists
- **Endpoint Path**: `/v1/marketing/lists`
- **Pagination**: Cursor with `page_size` of 1000

### Segments
- **Endpoint Path**: `/v1/marketing/segments/2.0`

### Singlesend Stats
- **Endpoint Path**: `/v1/marketing/stats/singlesends`
- **Pagination**: Cursor with `page_size` of 50

### Stats Automations
- **Endpoint Path**: `/v1/marketing/stats/automations`
- **Pagination**: Cursor with `page_size` of 50

### Singlesends
- **Endpoint Path**: `/v1/marketing/singlesends`
- **Pagination**: Cursor with `page_size` of 100

### Templates
- **Endpoint Path**: `/v1/templates`
- **Pagination**: Cursor with `page_size` of 200

### Campaigns
- **Endpoint Path**: `/v1/marketing/campaigns`
- **Pagination**: Cursor with `page_size` of 100

### Contacts
- **Endpoint Path**: `/v1/marketing/contacts/exports`
- **Pagination**: Cursor with `page_size` of 100

## Error Handling

- **Retry Logic**: Implement retry on status codes [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

- **Bounces**: Fields include `created`, `email`, `reason`, `status`
- **Spam Reports**: Fields include `created`, `email`, `ip`
- **Global Suppressions**: Fields include `created`, `email`
- **Blocks**: Fields include `created`, `email`, `reason`, `status`
- **Invalid Emails**: Fields include `created`, `email`, `reason`
- **Suppression Groups**: Fields include `description`, `id`, `is_default`, `name`, `unsubscribes`
- **Suppression Group Members**: Fields include `created_at`, `email`, `group_id`, `group_name`
- **Lists**: Fields include `_metadata`, `contact_count`, `id`, `name`
- **Segments**: Fields include `contacts_count`, `created_at`, `id`, `name`, `next_sample_update`, `parent_list_ids`, `query_version`, `sample_updated_at`, `status`, `updated_at`
- **Singlesend Stats**: Fields include `ab_phase`, `ab_variation`, `aggregation`, `id`, `stats`
- **Stats Automations**: Fields include `aggregation`, `id`, `stats`, `step_id`
- **Singlesends**: Fields include `categories`, `created_at`, `id`, `is_abtest`, `name`, `send_at`, `status`, `updated_at`
- **Templates**: Fields include `generation`, `id`, `name`, `updated_at`, `versions`
- **Campaigns**: Fields include `channels`, `created_at`, `id`, `is_abtest`, `name`, `status`, `updated_at`
- **Contacts**: Fields include `address_line_1`, `address_line_2`, `alternate_emails`, `city`, `country`, `email`, `first_name`, `contact_id`, `last_name`, `postal_code`, `state_province_region`, `list_ids`, `created_at`, `updated_at`, `custom_fields`, `phone_number`, `whatsapp`, `line`, `facebook`, `unique_name`