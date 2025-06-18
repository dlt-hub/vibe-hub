# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and schema. The API uses bearer token authentication and supports pagination for data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "subscribers",
            "endpoint": {
                "path": "/v1/subscribers",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/v1/segments",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "automations",
            "endpoint": {
                "path": "/v1/automations",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 10,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "timezones",
            "endpoint": {
                "path": "/v1/timezones",
                "data_selector": "data"
            }
        },
        {
            "name": "forms_popup",
            "endpoint": {
                "path": "/v1/forms/popup",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "forms_embedded",
            "endpoint": {
                "path": "/v1/forms/embedded",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "forms_promotion",
            "endpoint": {
                "path": "/v1/forms/promotion",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_token"]`

## Resources

### Subscribers
- **Endpoint Path**: `/v1/subscribers`
- **Pagination**: Page number with limit of 25
- **Data Selector**: `data`

### Segments
- **Endpoint Path**: `/v1/segments`
- **Pagination**: Page number with limit of 50
- **Data Selector**: `data`

### Automations
- **Endpoint Path**: `/v1/automations`
- **Pagination**: Page number with limit of 10
- **Data Selector**: `data`

### Campaigns
- **Endpoint Path**: `/v1/campaigns`
- **Pagination**: Page number with limit of 25
- **Data Selector**: `data`

### Timezones
- **Endpoint Path**: `/v1/timezones`
- **Data Selector**: `data`

### Forms Popup
- **Endpoint Path**: `/v1/forms/popup`
- **Pagination**: Page number with limit of 25
- **Data Selector**: `data`

### Forms Embedded
- **Endpoint Path**: `/v1/forms/embedded`
- **Pagination**: Page number with limit of 25
- **Data Selector**: `data`

### Forms Promotion
- **Endpoint Path**: `/v1/forms/promotion`
- **Pagination**: Page number with limit of 25
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Not specified in the YAML, but can be configured as needed.
- **HTTP Status Codes**: Not specified in the YAML, but common retry status codes include 429, 500, 502, 503, 504.

## Schema

Each resource has its own schema, which includes fields such as `id`, `name`, `created_at`, and other relevant data points. The schemas are designed to be flexible and include additional properties as needed. Field names are generalized to ensure vendor neutrality.