# Overview

This document provides a configuration guide for integrating with a third-party event management API. The integration allows users to sync data such as event details, ticket sales, and customer information into their data warehouse or analytics tools. The API supports automated data extraction for efficient reporting and data-driven insights across managed events.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_token"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "organizers",
            "endpoint": {
                "path": "/organizers",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/organizers/{{ stream_partition.organizer }}/events",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "tax_rules",
            "endpoint": {
                "path": "/organizers/{{ stream_partition.organizer }}/events/{{ stream_partition.event }}/taxrules",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `dlt.secrets["api_token"]` to securely store and access the API token.
- **Location**: The API key is injected into the request header.

# Resources

Each resource represents a different data entity available from the API. Below are the configurations for some of the resources:

- **Organizers**
  - **Endpoint Path**: `/organizers`
  - **Primary Key**: `slug`
  - **Pagination Strategy**: Page number with parameters `page` and `page_size`
  - **Data Selector**: `results`

- **Events**
  - **Endpoint Path**: `/organizers/{{ stream_partition.organizer }}/events`
  - **Primary Key**: `slug`
  - **Pagination Strategy**: Page number with parameters `page` and `page_size`
  - **Data Selector**: `results`

- **Tax Rules**
  - **Endpoint Path**: `/organizers/{{ stream_partition.organizer }}/events/{{ stream_partition.event }}/taxrules`
  - **Primary Key**: `id`
  - **Pagination Strategy**: Page number with parameters `page` and `page_size`
  - **Data Selector**: `results`

# Error Handling

- **Retry Logic**: The API will retry requests on HTTP status code 400.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined to ensure data consistency and integrity. Below are examples of schema fields for some resources:

- **Organizers**
  - `name`: string or null
  - `public_url`: string or null
  - `slug`: string (required)

- **Events**
  - `currency`: string or null
  - `date_from`: string or null
  - `slug`: string (required)

- **Tax Rules**
  - `id`: number (required)
  - `name`: object or null
  - `rate`: string or null

Each schema is designed to be flexible, allowing for additional properties and variations in data types where applicable.