# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports both full refresh and incremental data synchronization, allowing for efficient data extraction and management.

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
            "name": "accounts",
            "endpoint": {
                "path": "/v1/account",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "agents",
            "endpoint": {
                "path": "/v1/agents",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "since_id",
                    "limit": 100
                }
            }
        },
        {
            "name": "agent_timeline",
            "endpoint": {
                "path": "/v1/incremental/agent_timeline",
                "data_selector": "agent_timeline",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "end_time",
                    "limit": 1000
                }
            }
        },
        {
            "name": "bans",
            "endpoint": {
                "path": "/v1/bans",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "since_id",
                    "limit": 100
                }
            }
        },
        {
            "name": "chats",
            "endpoint": {
                "path": "/v1/incremental/chats",
                "data_selector": "chats",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "end_time",
                    "limit": 1000
                }
            }
        },
        {
            "name": "departments",
            "endpoint": {
                "path": "/v1/departments",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "goals",
            "endpoint": {
                "path": "/v1/goals",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/v1/roles",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "routing_settings",
            "endpoint": {
                "path": "/v1/routing_settings/account",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "shortcuts",
            "endpoint": {
                "path": "/v1/shortcuts",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "skills",
            "endpoint": {
                "path": "/v1/skills",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "triggers",
            "endpoint": {
                "path": "/v1/triggers",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `access_token` stored in `dlt.secrets`.

# Resources

## Accounts
- **Endpoint Path**: `/v1/account`
- **Primary Key**: `account_key`
- **Pagination**: Single page
- **Data Selector**: `data`

## Agents
- **Endpoint Path**: `/v1/agents`
- **Primary Key**: `id`
- **Pagination**: Cursor-based
  - **Cursor Parameter**: `since_id`
  - **Limit**: 100
- **Data Selector**: `data`

## Agent Timeline
- **Endpoint Path**: `/v1/incremental/agent_timeline`
- **Primary Key**: `id`
- **Pagination**: Cursor-based
  - **Cursor Parameter**: `end_time`
  - **Limit**: 1000
- **Data Selector**: `agent_timeline`

## Bans
- **Endpoint Path**: `/v1/bans`
- **Primary Key**: `id`
- **Pagination**: Cursor-based
  - **Cursor Parameter**: `since_id`
  - **Limit**: 100
- **Data Selector**: `data`

## Chats
- **Endpoint Path**: `/v1/incremental/chats`
- **Primary Key**: `id`
- **Pagination**: Cursor-based
  - **Cursor Parameter**: `end_time`
  - **Limit**: 1000
- **Data Selector**: `chats`

## Departments
- **Endpoint Path**: `/v1/departments`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

## Goals
- **Endpoint Path**: `/v1/goals`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

## Roles
- **Endpoint Path**: `/v1/roles`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

## Routing Settings
- **Endpoint Path**: `/v1/routing_settings/account`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

## Shortcuts
- **Endpoint Path**: `/v1/shortcuts`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

## Skills
- **Endpoint Path**: `/v1/skills`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

## Triggers
- **Endpoint Path**: `/v1/triggers`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implemented with exponential backoff
- **Retry-On Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each resource's schema is consistent with the provided YAML configuration, using generic field names such as `customer_id` or `user_key` where applicable.