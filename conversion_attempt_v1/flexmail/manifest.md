# Overview

This document provides a configuration guide for integrating with a third-party email marketing API using dltHub's REST API source. The integration allows for seamless data synchronization of various resources such as contact details, custom fields, interests, segments, sources, and webhook events. This setup is ideal for businesses looking to centralize their marketing data for enhanced visibility and decision-making.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["account_id"],
            "password": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "_embedded.item",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/custom-fields",
                "data_selector": "_embedded.item"
            }
        },
        {
            "name": "interests",
            "endpoint": {
                "path": "/interests",
                "data_selector": "_embedded.item"
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/segments",
                "data_selector": "_embedded.item"
            }
        },
        {
            "name": "sources",
            "endpoint": {
                "path": "/sources",
                "data_selector": "_embedded.item",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "webhook_events",
            "endpoint": {
                "path": "/webhook-events",
                "data_selector": ""
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**:
  - `account_id`: Your account ID for the API.
  - `api_key`: A personal access token for API authentication.

## Resources

### Contacts
- **Endpoint Path**: `/contacts`
- **Pagination**: Offset-based with a limit of 500
- **Data Selector**: `_embedded.item`

### Custom Fields
- **Endpoint Path**: `/custom-fields`
- **Data Selector**: `_embedded.item`

### Interests
- **Endpoint Path**: `/interests`
- **Data Selector**: `_embedded.item`

### Segments
- **Endpoint Path**: `/segments`
- **Data Selector**: `_embedded.item`

### Sources
- **Endpoint Path**: `/sources`
- **Pagination**: Offset-based with a limit of 500
- **Data Selector**: `_embedded.item`

### Webhook Events
- **Endpoint Path**: `/webhook-events`
- **Data Selector**: None

## Error Handling

- **Retry Logic**: Implement retry on status codes 429, 500, 502, 503, 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

### Contacts
- **Fields**: `id` (number), `email` (string), `first_name` (string), `name` (string), `language` (string), `custom_fields` (object)

### Custom Fields
- **Fields**: `id` (string), `name` (string), `type` (string), `placeholder` (string)

### Interests
- **Fields**: `id` (string), `name` (string), `label` (string), `description` (string), `visibility` (string)

### Segments
- **Fields**: `id` (string), `name` (string), `number_of_contacts` (number)

### Sources
- **Fields**: `id` (number), `name` (string)

### Webhook Events
- **Fields**: `events` (array of strings)

This configuration provides a comprehensive setup for integrating with the API, ensuring efficient data synchronization and management.