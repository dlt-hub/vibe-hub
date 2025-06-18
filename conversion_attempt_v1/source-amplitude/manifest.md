# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for data retrieval from various resources, such as annotations, cohorts, session lengths, active users, and events. The API supports both full and incremental data synchronization.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["secret_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "annotations",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [400, 403, 404, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "cohorts",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "cohorts",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [400, 403, 404, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "average_session_length",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [400, 403, 404, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "active_users",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [400, 403, 404, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "events_list",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [400, 403, 404, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [400, 403, 404, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: Your API key
  - `secret_key`: Your secret key

## Resources

### Annotations
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: Single page

### Cohorts
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `cohorts`
- **Primary Key**: `id`
- **Pagination**: Single page

### Average Session Length
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `data`
- **Primary Key**: `date`
- **Pagination**: Single page

### Active Users
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `data`
- **Primary Key**: `date`
- **Pagination**: Single page

### Events List
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: Single page

### Events
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `data`
- **Primary Key**: `uuid`
- **Pagination**: Single page

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: 400, 403, 404, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

### Annotations
- **Fields**: 
  - `date`: string, nullable
  - `details`: string, nullable
  - `id`: integer, nullable
  - `label`: string, nullable

### Cohorts
- **Fields**: 
  - `appId`: integer, nullable
  - `archived`: boolean, nullable
  - `definition`: object, nullable
  - `description`: string, nullable
  - `finished`: boolean, nullable
  - `id`: string, nullable
  - `name`: string, nullable
  - `owners`: array of strings, nullable
  - `published`: boolean, nullable
  - `size`: integer, nullable
  - `type`: string, nullable
  - `lastMod`: integer, nullable
  - `lastComputed`: integer, nullable
  - `hidden`: boolean, nullable
  - `is_predictive`: boolean, nullable
  - `is_official_content`: boolean, nullable
  - `chart_id`: string, nullable
  - `createdAt`: integer, nullable
  - `edit_id`: string, nullable
  - `last_viewed`: integer, nullable
  - `location_id`: string, nullable
  - `metadata`: array of strings, nullable
  - `popularity`: integer, nullable
  - `shortcut_ids`: array of strings, nullable
  - `view_count`: integer, nullable
  - `viewers`: array of strings, nullable

### Average Session Length
- **Fields**: 
  - `date`: string, nullable
  - `length`: number, nullable

### Active Users
- **Fields**: 
  - `date`: string, nullable
  - `statistics`: object, nullable

### Events List
- **Fields**: 
  - `autohidden`: boolean, nullable
  - `clusters_hidden`: boolean, nullable
  - `deleted`: boolean, nullable
  - `display`: string, nullable
  - `flow_hidden`: boolean, nullable
  - `hidden`: boolean, nullable
  - `id`: number
  - `in_waitroom`: boolean, nullable
  - `name`: string, nullable
  - `non_active`: boolean, nullable
  - `timeline_hidden`: boolean, nullable
  - `totals`: number, nullable
  - `totals_delta`: number, nullable
  - `value`: string, nullable

### Events
- **Fields**: 
  - `server_received_time`: string, nullable
  - `app`: integer, nullable
  - `device_carrier`: string, nullable
  - `city`: string, nullable
  - `user_id`: string, nullable
  - `uuid`: string, nullable
  - `event_time`: string, nullable
  - `platform`: string, nullable
  - `os_version`: string, nullable
  - `processed_time`: string, nullable
  - `user_creation_time`: string, nullable
  - `version_name`: string, nullable
  - `ip_address`: string, nullable
  - `paying`: boolean, nullable
  - `dma`: string, nullable
  - `group_properties`: object, nullable
  - `user_properties`: object, nullable
  - `client_upload_time`: string, nullable
  - `event_type`: string, nullable
  - `library`: string, nullable
  - `device_type`: string, nullable
  - `device_manufacturer`: string, nullable
  - `start_version`: string, nullable
  - `location_lng`: number, nullable
  - `server_upload_time`: string, nullable
  - `event_id`: integer, nullable
  - `location_lat`: number, nullable
  - `os_name`: string, nullable
  - `device_brand`: string, nullable
  - `groups`: object, nullable
  - `event_properties`: object, nullable
  - `data`: object, nullable
  - `device_id`: string, nullable
  - `language`: string, nullable
  - `device_model`: string, nullable
  - `country`: string, nullable
  - `region`: string, nullable
  - `is_attribution_event`: boolean, nullable
  - `adid`: string, nullable
  - `session_id`: number, nullable
  - `device_family`: string, nullable
  - `sample_rate`: string or number, nullable
  - `idfa`: string, nullable
  - `client_event_time`: string, nullable
  - `data_type`: string, nullable
  - `plan`: object, nullable
  - `source_id`: string, nullable
  - `partner_id`: string, nullable
  - `global_user_properties`: object, nullable

This configuration guide provides a comprehensive setup for integrating with a third-party analytics API using dltHub's REST API source. The guide ensures a clean, vendor-neutral approach to data integration, focusing on reusable patterns and public configurations.