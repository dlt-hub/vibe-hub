# Overview

This document provides a configuration example for integrating with a third-party incident management API using the dltHub REST API source. The integration supports fetching data from various resources such as incidents, incident logs, teams, services, users, and priorities. The configuration is designed to be vendor-neutral and can be adapted to similar APIs with minimal changes.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
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
            "name": "incidents",
            "endpoint": {
                "path": "/incidents",
                "data_selector": "incidents"
            }
        },
        {
            "name": "incident_logs",
            "endpoint": {
                "path": "/incidents/{{ stream_partition.incident_id }}/log_entries",
                "data_selector": "log_entries"
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/teams",
                "data_selector": "teams"
            }
        },
        {
            "name": "services",
            "endpoint": {
                "path": "/services",
                "data_selector": "services"
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "users"
            }
        },
        {
            "name": "priorities",
            "endpoint": {
                "path": "/priorities",
                "data_selector": "priorities"
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`
- **Location**: Header with the name `Authorization`

## Resources

### Incidents
- **Endpoint Path**: `/incidents`
- **Data Selector**: `incidents`
- **Primary Key**: `id`

### Incident Logs
- **Endpoint Path**: `/incidents/{{ stream_partition.incident_id }}/log_entries`
- **Data Selector**: `log_entries`
- **Primary Key**: `id`

### Teams
- **Endpoint Path**: `/teams`
- **Data Selector**: `teams`
- **Primary Key**: `id`

### Services
- **Endpoint Path**: `/services`
- **Data Selector**: `services`
- **Primary Key**: `id`

### Users
- **Endpoint Path**: `/users`
- **Data Selector**: `users`
- **Primary Key**: `id`

### Priorities
- **Endpoint Path**: `/priorities`
- **Data Selector**: `priorities`
- **Primary Key**: `id`

## Error Handling

- **Retry Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **HTTP Status Codes**: Retry on status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: Configurable with a default of 5 retries.

## Schema

The schema for each resource is defined with neutral field names and types. The fields are consistent with the data structure provided by the API and are designed to be adaptable to similar APIs. Each resource has a primary key field named `id` to ensure data integrity and uniqueness.