# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports retrieving data from various resources such as users, time logs, tags, projects, and clients. The API uses a custom authentication method and supports basic data retrieval without pagination.

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
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "time_logs",
            "endpoint": {
                "path": "/v1/time_logs",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/tags",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "clients",
            "endpoint": {
                "path": "/v1/clients",
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

- **Type**: Basic Authentication
- **Secrets**: 
  - `email`: The username for authentication.
  - `password`: The password associated with the username.

# Resources

## Users
- **Endpoint Path**: `/v1/users`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

## Time Logs
- **Endpoint Path**: `/v1/time_logs`
- **Primary Key**: `logId`
- **Pagination**: Single page
- **Data Selector**: `data`

## Tags
- **Endpoint Path**: `/v1/tags`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

## Projects
- **Endpoint Path**: `/v1/projects`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

## Clients
- **Endpoint Path**: `/v1/clients`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Not specified in the configuration.
- **Known HTTP Response Codes**: Not specified in the configuration.
- **Fallback/Handling Behavior**: Not specified in the configuration.

# Schema

The schema for each resource is defined with neutral field names and types. Each field can be nullable and supports various data types such as number, string, boolean, and array. The schema is consistent with the provided YAML configuration and uses neutral field names.