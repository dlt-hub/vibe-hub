# Overview

This document provides a configuration guide for integrating with a third-party communication API. The integration allows for seamless data synchronization of user information, call logs, SMS messages, contacts, phone numbers, and agent analytics. This setup ensures that businesses can centralize their communication data for enhanced reporting and analysis.

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
            "name": "users",
            "endpoint": {
                "path": "/v2.1/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "calls",
            "endpoint": {
                "path": "/v2.1/calls",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "sms",
            "endpoint": {
                "path": "/v2.1/texts",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts/list",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "phone_numbers",
            "endpoint": {
                "path": "/v1/numbers/list",
                "data_selector": "data"
            }
        },
        {
            "name": "agent_analytics",
            "endpoint": {
                "path": "/v2.1/sales_dialer/analytics",
                "data_selector": "data"
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets` for secure access.
- **Location**: The API key is injected into the request header.

## Resources

### Users
- **Endpoint Path**: `/v2.1/users`
- **Pagination Strategy**: Page number with parameters `page` and `per_page`
- **Data Selector**: `data`

### Calls
- **Endpoint Path**: `/v2.1/calls`
- **Pagination Strategy**: Page number with parameters `page` and `per_page`
- **Data Selector**: `data`

### SMS
- **Endpoint Path**: `/v2.1/texts`
- **Pagination Strategy**: Page number with parameters `page` and `per_page`
- **Data Selector**: `data`

### Contacts
- **Endpoint Path**: `/v1/contacts/list`
- **Pagination Strategy**: Page number with parameters `page` and `per_page`
- **Data Selector**: `data`

### Phone Numbers
- **Endpoint Path**: `/v1/numbers/list`
- **Data Selector**: `data`

### Agent Analytics
- **Endpoint Path**: `/v2.1/sales_dialer/analytics`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

### Users
- **Fields**: Includes `id`, `email`, `name`, `created_at`, etc.
- **Primary Key**: `id`

### Calls
- **Fields**: Includes `id`, `call_date`, `call_duration`, `agent_id`, etc.
- **Primary Key**: `id`

### SMS
- **Fields**: Includes `id`, `sms_date`, `agent_id`, `contact_number`, etc.
- **Primary Key**: `id`

### Contacts
- **Fields**: Includes `id`, `email`, `firstname`, `lastname`, etc.
- **Primary Key**: `id`

### Phone Numbers
- **Fields**: Includes `id`, `phone`, `friendly_name`, etc.
- **Primary Key**: `id`

### Agent Analytics
- **Fields**: Includes `agent_id`, `agent_email`, `answered_calls`, etc.
- **Primary Key**: `agent_id`

This configuration ensures a robust and flexible integration with the third-party communication API, allowing for efficient data synchronization and management.