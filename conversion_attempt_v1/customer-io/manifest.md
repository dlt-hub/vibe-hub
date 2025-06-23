# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports retrieving data from various resources such as campaigns, campaign actions, and newsletters. The API uses bearer token authentication and supports GET requests to fetch data.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
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
            "name": "campaigns",
            "endpoint": {
                "path": "/campaigns",
                "data_selector": "campaigns"
            }
        },
        {
            "name": "campaigns_actions",
            "endpoint": {
                "path": "/campaigns/{parent_id}/actions",
                "data_selector": "actions"
            }
        },
        {
            "name": "newsletters",
            "endpoint": {
                "path": "/newsletters",
                "data_selector": "newsletters"
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer Token
- **Token**: Stored securely in `dlt.secrets` under the key `api_key`.

# Resources

### Campaigns
- **Endpoint Path**: `/campaigns`
- **HTTP Method**: GET
- **Data Selector**: `campaigns`
- **Primary Key**: `id`

### Campaigns Actions
- **Endpoint Path**: `/campaigns/{parent_id}/actions`
- **HTTP Method**: GET
- **Data Selector**: `actions`
- **Primary Key**: `id`

### Newsletters
- **Endpoint Path**: `/newsletters`
- **HTTP Method**: GET
- **Data Selector**: `newsletters`
- **Primary Key**: `id`

# Error Handling

- **Retry Logic**: Implemented with exponential backoff for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types. Below is a generalized example for the `campaigns` resource:

- **type**: `string` or `null`
- **actions**: Array of objects with properties:
  - **type**: `string` or `null`
  - **id**: `integer` or `null`
- **active**: `boolean` or `null`
- **created**: `integer` or `null`
- **created_by**: `string` or `null`
- **date_attribute**: `string` or `null`
- **deduplicate_id**: `string` or `null`
- **event_name**: `string` or `null`
- **first_started**: `integer` or `null`
- **frequency**: `string` or `null`
- **id**: `integer` or `null`
- **msg_templates**: Array of objects with properties:
  - **type**: `string` or `null`
  - **id**: `integer` or `null`
- **name**: `string` or `null`
- **start_hour**: `integer` or `null`
- **start_minutes**: `integer` or `null`
- **state**: `string` or `null`
- **tags**: Array with properties:
  - **id**: `integer` or `null`
- **timezone**: `string` or `null`
- **trigger_segment_ids**: Array with properties:
  - **id**: `integer` or `null`
- **updated**: `integer` or `null`
- **use_customer_timezone**: `boolean` or `null`

This configuration ensures a clean, vendor-neutral integration with the third-party analytics API, following dltHub's REST API source format.