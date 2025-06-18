# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and configuration settings. The API uses a cursor-based pagination strategy and supports incremental synchronization based on datetime fields.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "accounts",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "account_coupon_redemptions",
            "endpoint": {
                "path": "/v1/accounts/{{ stream_partition['account_id'] }}/coupon_redemptions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "account_notes",
            "endpoint": {
                "path": "/v1/accounts/{{ stream_partition['account_id'] }}/notes",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "add_ons",
            "endpoint": {
                "path": "/v1/add_ons",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "billing_infos",
            "endpoint": {
                "path": "/v1/accounts/{{ stream_partition['account_id'] }}/billing_infos",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "coupons",
            "endpoint": {
                "path": "/v1/coupons",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "credit_payments",
            "endpoint": {
                "path": "/v1/credit_payments",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "export_dates",
            "endpoint": {
                "path": "/v1/export_dates",
                "data_selector": "data"
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/v1/invoices",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "line_items",
            "endpoint": {
                "path": "/v1/line_items",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "measured_units",
            "endpoint": {
                "path": "/v1/measured_units",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "plans",
            "endpoint": {
                "path": "/v1/plans",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "shipping_addresses",
            "endpoint": {
                "path": "/v1/accounts/{{ stream_partition['account_id'] }}/shipping_addresses",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "shipping_methods",
            "endpoint": {
                "path": "/v1/shipping_methods",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "subscriptions",
            "endpoint": {
                "path": "/v1/subscriptions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v1/transactions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        },
        {
            "name": "unique_coupons",
            "endpoint": {
                "path": "/v1/coupons/{{ stream_partition['coupon_id'] }}/unique_coupon_codes",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_value": "{{ response.next }}",
                    "stop_condition": "{{ response.has_more is false }}"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic
- **Credentials**: Uses an API key stored in `dlt.secrets["api_key"]`.

# Resources

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized paths for each resource.
- **HTTP Method**: GET (default for all resources).
- **Pagination Strategy**: Cursor-based with a limit of 200 records per page.
- **Data Selector**: Extracts data from the "data" field in the response.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Fields**: Generalized field names and types.
- **Primary Key**: "id" for all resources.
- **Write Disposition**: Merge strategy for data updates.