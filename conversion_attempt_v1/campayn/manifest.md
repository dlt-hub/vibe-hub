# Overview

This document provides a configuration guide for integrating with a third-party email marketing API using dltHub's REST API source. The integration allows for seamless data extraction of subscriber lists, forms, contacts, emails, and reports, enabling businesses to centralize their marketing insights and optimize strategies.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
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
            "name": "subscriber_lists",
            "endpoint": {
                "path": "/lists.json",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "forms",
            "endpoint": {
                "path": "/lists/{{ list_id }}/forms.json",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/lists/{{ list_id }}/contacts.json",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "emails",
            "endpoint": {
                "path": "/emails.json",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "reports",
            "endpoint": {
                "path": "/reports/calendar.json",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: 
  - `api_key`: The API key used for authentication, stored securely in dlt's secrets.

## Resources

### Subscriber Lists
- **Endpoint Path**: `/lists.json`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

### Forms
- **Endpoint Path**: `/lists/{{ list_id }}/forms.json`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

### Contacts
- **Endpoint Path**: `/lists/{{ list_id }}/contacts.json`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

### Emails
- **Endpoint Path**: `/emails.json`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

### Reports
- **Endpoint Path**: `/reports/calendar.json`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Subscriber Lists
- **Fields**:
  - `id`: string
  - `contact_count`: number or null
  - `list_name`: string or null
  - `tags`: string or null

### Forms
- **Fields**:
  - `id`: string
  - `contact_list_id`: string or null
  - `form_html`: string or null
  - `form_title`: string or null
  - `form_type`: string or null
  - `signup_count`: string or null

### Contacts
- **Fields**:
  - `id`: string
  - `confirmed`: string or null
  - `email`: string or null
  - `first_name`: string or null
  - `last_name`: string or null
  - `image_url`: string or null

### Emails
- **Fields**:
  - `id`: string
  - `name`: string or null
  - `percent_responses`: number or null
  - `percent_views`: number or null
  - `preview_thumb`: string or null
  - `preview_url`: string or null
  - `send_count`: string or null
  - `send_now`: boolean or null
  - `status`: string or null
  - `unique_responses`: number or null
  - `unique_views`: number or null

### Reports
- **Fields**:
  - `id`: string
  - `name`: string or null
  - `preview_url`: string or null
  - `scheduled_date`: string or null
  - `status`: string or null

This configuration provides a comprehensive setup for integrating with a generic email marketing API, ensuring data is extracted and managed efficiently within your data systems.