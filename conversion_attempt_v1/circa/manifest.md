# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The integration allows for seamless data extraction from the API, facilitating automated data integration into your data warehouse or analytics systems. This setup is ideal for organizations looking to consolidate data from various sources for comprehensive business intelligence.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
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
                "path": "/teams",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/events",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/companies",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "event_fields",
            "endpoint": {
                "path": "/fields",
                "data_selector": "data",
                "params": {
                    "fields_for": "Event"
                }
            }
        },
        {
            "name": "company_fields",
            "endpoint": {
                "path": "/fields",
                "data_selector": "data",
                "params": {
                    "fields_for": "Company"
                }
            }
        },
        {
            "name": "contact_fields",
            "endpoint": {
                "path": "/fields",
                "data_selector": "data",
                "params": {
                    "fields_for": "Contact"
                }
            }
        },
        {
            "name": "event_contacts",
            "endpoint": {
                "path": "/events/{event_id}/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "company_contacts",
            "endpoint": {
                "path": "/companies/{company_id}/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Teams
- **Endpoint Path**: `/teams`
- **Pagination**: Page number with a limit of 25
- **Data Selector**: `data`

### Events
- **Endpoint Path**: `/events`
- **Pagination**: Page number with a limit of 25
- **Data Selector**: `data`

### Contacts
- **Endpoint Path**: `/contacts`
- **Pagination**: Page number with a limit of 25
- **Data Selector**: `data`

### Companies
- **Endpoint Path**: `/companies`
- **Pagination**: Page number with a limit of 25
- **Data Selector**: `data`

### Event Fields
- **Endpoint Path**: `/fields`
- **Parameters**: `fields_for=Event`
- **Data Selector**: `data`

### Company Fields
- **Endpoint Path**: `/fields`
- **Parameters**: `fields_for=Company`
- **Data Selector**: `data`

### Contact Fields
- **Endpoint Path**: `/fields`
- **Parameters**: `fields_for=Contact`
- **Data Selector**: `data`

### Event Contacts
- **Endpoint Path**: `/events/{event_id}/contacts`
- **Pagination**: Page number with a limit of 25
- **Data Selector**: `data`

### Company Contacts
- **Endpoint Path**: `/companies/{company_id}/contacts`
- **Pagination**: Page number with a limit of 25
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry on status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

- **Teams**: Includes fields like `id`, `name`
- **Events**: Includes fields like `id`, `name`, `updated_at`
- **Contacts**: Includes fields like `id`, `email`, `updated_at`
- **Companies**: Includes fields like `id`, `name`, `updated_at`
- **Event Fields**: Includes fields like `id`, `label`
- **Company Fields**: Includes fields like `id`, `label`
- **Contact Fields**: Includes fields like `id`, `label`
- **Event Contacts**: Includes fields like `id`, `email`
- **Company Contacts**: Includes fields like `id`, `email`

This configuration provides a comprehensive setup for integrating with a third-party analytics API, ensuring efficient data extraction and integration into your data systems.