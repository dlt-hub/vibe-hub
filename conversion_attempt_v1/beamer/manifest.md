# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from a resource endpoint, utilizing pagination and incremental sync based on datetime fields.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v0/",
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
            "name": "nps_data",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "maxResults",
                    "limit": 100,
                    "start_from_page": 0
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 5,
                    "backoff_factor": 5
                }
            },
            "incremental_sync": {
                "type": "datetime",
                "cursor_field": "date",
                "start_datetime": dlt.config["start_date"],
                "end_datetime": "{{ now_utc().strftime('%Y-%m-%dT%H:%M:%SZ') }}",
                "datetime_format": "%Y-%m-%dT%H:%M:%SZ",
                "start_time_option": {
                    "field_name": "dateFrom",
                    "inject_into": "query"
                },
                "end_time_option": {
                    "field_name": "dateTo",
                    "inject_into": "query"
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

- **Resource Name**: nps_data
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: Page number-based
  - **Page Parameter**: `page`
  - **Limit Parameter**: `maxResults`
  - **Limit**: 100
  - **Start From Page**: 0
- **Data Selector**: `data`

## Error Handling

- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 5
- **Backoff Factor**: 5

## Schema

The schema for the resource includes the following fields:

- **browser**: string or null
- **city**: string or null
- **country**: string or null
- **date**: string (required)
- **feedback**: string or null
- **filter**: string or null
- **id**: string or null
- **language**: string or null
- **origin**: string or null
- **os**: string or null
- **refUrl**: string or null
- **score**: number or null
- **url**: string or null
- **userEmail**: string or null
- **userFirstName**: string or null
- **userId**: string or null
- **userLastName**: string or null

This configuration provides a clean and generalized setup for integrating with a third-party analytics API using dltHub's REST API source format, ensuring a vendor-neutral approach.