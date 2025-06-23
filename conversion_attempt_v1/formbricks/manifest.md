# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration allows for seamless data synchronization, enabling enhanced analytics and insights into user behavior and satisfaction across platforms.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
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
            "name": "surveys",
            "endpoint": {
                "path": "/v1/management/surveys",
                "data_selector": "data"
            }
        },
        {
            "name": "action_classes",
            "endpoint": {
                "path": "/v1/management/action-classes",
                "data_selector": "data"
            }
        },
        {
            "name": "attribute_classes",
            "endpoint": {
                "path": "/v1/management/attribute-classes",
                "data_selector": "data"
            }
        },
        {
            "name": "identified_peoples",
            "endpoint": {
                "path": "/v1/management/people",
                "data_selector": "data"
            }
        },
        {
            "name": "responses",
            "endpoint": {
                "path": "/v1/management/responses",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/v1/webhooks",
                "data_selector": "data"
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: The API key is stored securely and injected into the request header as `X-API-Key`.

## Resources

### Surveys
- **Endpoint Path**: `/v1/management/surveys`
- **Data Selector**: `data`
- **Primary Key**: `id`

### Action Classes
- **Endpoint Path**: `/v1/management/action-classes`
- **Data Selector**: `data`
- **Primary Key**: `id`

### Attribute Classes
- **Endpoint Path**: `/v1/management/attribute-classes`
- **Data Selector**: `data`
- **Primary Key**: `id`

### Identified Peoples
- **Endpoint Path**: `/v1/management/people`
- **Data Selector**: `data`
- **Primary Key**: `id`

### Responses
- **Endpoint Path**: `/v1/management/responses`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination Strategy**: Offset
  - **Limit**: 100
  - **Offset Parameter**: `skip`

### Webhooks
- **Endpoint Path**: `/v1/webhooks`
- **Data Selector**: `data`
- **Primary Key**: `id`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with various data structures. Each resource includes a primary key field named `id`, which is required for data integrity and uniqueness.