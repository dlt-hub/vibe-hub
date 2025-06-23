# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as teams, groups, members, and collections, with capabilities for incremental synchronization and error handling.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "teams",
            "endpoint": {
                "path": "/v1/teams",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "token"
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
                    "cursor_param": "token"
                }
            }
        },
        {
            "name": "members",
            "endpoint": {
                "path": "/v1/members",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "token"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: Your API username
  - `password`: Your API password

## Resources

### Teams
- **Endpoint Path**: `/v1/teams`
- **Primary Key**: `id`
- **Pagination**: Cursor-based using `token` parameter
- **Data Selector**: `data`

### Groups
- **Endpoint Path**: `/v1/groups`
- **Primary Key**: `id`
- **Pagination**: Cursor-based using `token` parameter
- **Data Selector**: `data`

### Members
- **Endpoint Path**: `/v1/members`
- **Primary Key**: `id`
- **Pagination**: Cursor-based using `token` parameter
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429
  - Maximum retries: 3
  - Backoff strategy: Exponential with a factor of 2
- **Error Messages**: Generalized rate limit messages

## Schema

### Teams Schema
- **Fields**:
  - `id`: string
  - `dateCreated`: string
  - `name`: string (nullable)
  - `description`: string (nullable)
  - `status`: string (nullable)

### Groups Schema
- **Fields**:
  - `id`: string
  - `dateCreated`: string
  - `name`: string (nullable)
  - `groupIdentifier`: string (nullable)

### Members Schema
- **Fields**:
  - `id`: string
  - `dateCreated`: string
  - `user`: object (nullable)
    - `email`: string (nullable)
    - `firstName`: string (nullable)
    - `lastName`: string (nullable)

This configuration provides a clean and vendor-neutral setup for accessing and synchronizing data from a third-party analytics API using dltHub's REST API source.