# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources such as countries, currencies, devices, timezones, traffic sources, user contexts, conversion types, conversion metrics, and partnership models. The API uses a cursor-based pagination strategy and requires basic authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["application_key"],
            "password": dlt.secrets["user_api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "countries",
            "endpoint": {
                "path": "/reference/country",
                "data_selector": "countries",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('hypermedia', {}).get('pagination', {}).get('next_page') or response.get('cursor_id') }}",
                    "stop_condition": "{{ not response.get('hypermedia', {}).get('pagination', {}).get('next_page') and not response.get('cursor_id') }}"
                }
            }
        },
        {
            "name": "currencies",
            "endpoint": {
                "path": "/reference/currency",
                "data_selector": "currencies",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('hypermedia', {}).get('pagination', {}).get('next_page') or response.get('cursor_id') }}",
                    "stop_condition": "{{ not response.get('hypermedia', {}).get('pagination', {}).get('next_page') and not response.get('cursor_id') }}"
                }
            }
        },
        {
            "name": "devices",
            "endpoint": {
                "path": "/reference/device",
                "data_selector": "devices",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('hypermedia', {}).get('pagination', {}).get('next_page') or response.get('cursor_id') }}",
                    "stop_condition": "{{ not response.get('hypermedia', {}).get('pagination', {}).get('next_page') and not response.get('cursor_id') }}"
                }
            }
        },
        {
            "name": "timezones",
            "endpoint": {
                "path": "/reference/timezone",
                "data_selector": "timezones",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('hypermedia', {}).get('pagination', {}).get('next_page') or response.get('cursor_id') }}",
                    "stop_condition": "{{ not response.get('hypermedia', {}).get('pagination', {}).get('next_page') and not response.get('cursor_id') }}"
                }
            }
        },
        {
            "name": "traffic_sources",
            "endpoint": {
                "path": "/reference/traffic_source",
                "data_selector": "traffic_sources",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('hypermedia', {}).get('pagination', {}).get('next_page') or response.get('cursor_id') }}",
                    "stop_condition": "{{ not response.get('hypermedia', {}).get('pagination', {}).get('next_page') and not response.get('cursor_id') }}"
                }
            }
        },
        {
            "name": "user_context",
            "endpoint": {
                "path": "/reference/user_context",
                "data_selector": "user_contexts",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('hypermedia', {}).get('pagination', {}).get('next_page') or response.get('cursor_id') }}",
                    "stop_condition": "{{ not response.get('hypermedia', {}).get('pagination', {}).get('next_page') and not response.get('cursor_id') }}"
                }
            }
        },
        {
            "name": "conversion_type",
            "endpoint": {
                "path": "/reference/conversion_type",
                "data_selector": "conversion_types",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('hypermedia', {}).get('pagination', {}).get('next_page') or response.get('cursor_id') }}",
                    "stop_condition": "{{ not response.get('hypermedia', {}).get('pagination', {}).get('next_page') and not response.get('cursor_id') }}"
                }
            }
        },
        {
            "name": "conversion_metrics",
            "endpoint": {
                "path": "/reference/conversion_metric",
                "data_selector": "conversion_metrics",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('hypermedia', {}).get('pagination', {}).get('next_page') or response.get('cursor_id') }}",
                    "stop_condition": "{{ not response.get('hypermedia', {}).get('pagination', {}).get('next_page') and not response.get('cursor_id') }}"
                }
            }
        },
        {
            "name": "partnership_model",
            "endpoint": {
                "path": "/reference/partnership_model",
                "data_selector": "partnership_models",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('hypermedia', {}).get('pagination', {}).get('next_page') or response.get('cursor_id') }}",
                    "stop_condition": "{{ not response.get('hypermedia', {}).get('pagination', {}).get('next_page') and not response.get('cursor_id') }}"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `application_key`: The application key for network identification.
  - `user_api_key`: The user API key for user identification.

# Resources

- **Resource Names**: countries, currencies, devices, timezones, traffic_sources, user_context, conversion_type, conversion_metrics, partnership_model
- **Endpoint Paths**: 
  - `/reference/country`
  - `/reference/currency`
  - `/reference/device`
  - `/reference/timezone`
  - `/reference/traffic_source`
  - `/reference/user_context`
  - `/reference/conversion_type`
  - `/reference/conversion_metric`
  - `/reference/partnership_model`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based
- **Data Selector**: Specific to each resource (e.g., "countries" for the countries resource)

# Error Handling

- **Retry Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP errors such as 429 (Too Many Requests) and 500-series server errors.
- **Fallback Behavior**: Stop pagination when no further pages are available.

# Schema

- **Fields**: Each resource has its own schema with fields such as `ref_country_id`, `currency_id`, `ref_device_id`, etc.
- **Types**: Fields can be of types like number, string, or null.
- **Nullability**: Fields can be nullable, as indicated in the schema.