# Overview

This document provides a configuration example for integrating with a third-party API using the dltHub REST API source. The integration supports various resources related to business hours, call settings, and more, allowing for data extraction and synchronization.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["auth_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "user_business_hours",
            "endpoint": {
                "path": "/account/{account_id}/extension/{extension_id}/business-hours",
                "data_selector": "schedule"
            }
        },
        {
            "name": "company_business_hours",
            "endpoint": {
                "path": "/account/{account_id}/business-hours",
                "data_selector": "schedule"
            }
        },
        {
            "name": "callblock_settings",
            "endpoint": {
                "path": "/account/{account_id}/extension/{extension_id}/caller-blocking",
                "data_selector": "greetings"
            }
        },
        {
            "name": "blocked_allowed_phonenumbers",
            "endpoint": {
                "path": "/account/{account_id}/extension/{extension_id}/caller-blocking/phone-numbers",
                "data_selector": "records"
            }
        },
        {
            "name": "forwarding_numbers",
            "endpoint": {
                "path": "/account/{account_id}/extension/{extension_id}/forwarding-number",
                "data_selector": "records",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "company_call_handling_rules",
            "endpoint": {
                "path": "/account/{account_id}/answering-rule",
                "data_selector": "records",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "user_call_records",
            "endpoint": {
                "path": "/account/{account_id}/extension/{extension_id}/call-log",
                "data_selector": "records",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "user_active_calls",
            "endpoint": {
                "path": "/account/{account_id}/extension/{extension_id}/active-calls",
                "data_selector": "records",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "call_monitoring_groups",
            "endpoint": {
                "path": "/account/{account_id}/call-monitoring-groups",
                "data_selector": "records",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "call_queues",
            "endpoint": {
                "path": "/account/{account_id}/call-queues",
                "data_selector": "records",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "call_record_settings",
            "endpoint": {
                "path": "/account/{account_id}/call-recording",
                "data_selector": "greetings"
            }
        },
        {
            "name": "greetings",
            "endpoint": {
                "path": "/dictionary/greeting",
                "data_selector": "records",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "ivr_prompts",
            "endpoint": {
                "path": "/dictionary/greeting",
                "data_selector": "records",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "fax_cover",
            "endpoint": {
                "path": "/dictionary/fax-cover-page",
                "data_selector": "records",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["auth_token"]`

# Resources

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: Generalized paths with placeholders for account and extension IDs
- **HTTP Method**: GET (default)
- **Data Selector**: Extracts data from specified fields
- **Pagination Strategy**: Uses page number pagination with parameters for page and limit

# Error Handling

- **Retry Logic**: Not explicitly defined in the YAML, but can be configured in dltHub with retry_on_status_codes and backoff_factor if needed.

# Schema

- **Fields**: Generalized field names and types
- **Types**: Includes nullability and object types
- **Additional Properties**: Allowed for flexibility in schema

This configuration provides a clean, vendor-neutral setup for integrating with a third-party API using dltHub's REST API source. It abstracts away proprietary details while maintaining the necessary structure for data extraction and synchronization.