# Overview

This document provides a configuration guide for integrating with a third-party event management API. The integration allows for the extraction of key event data, including details on events, products, vouchers, discounts, check-ins, issued tickets, orders, and waitlists. This setup enables businesses to analyze ticket sales, attendance, and customer interactions, streamlining event management insights.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
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
            "name": "events",
            "endpoint": {
                "path": "/event_series/{event_series_id}/events",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "starting_after"
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/orders",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "starting_after"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/products",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "starting_after"
                }
            }
        },
        {
            "name": "vouchers",
            "endpoint": {
                "path": "/vouchers",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "starting_after"
                }
            }
        },
        {
            "name": "check_ins",
            "endpoint": {
                "path": "/check_ins",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "starting_after"
                }
            }
        },
        {
            "name": "discounts",
            "endpoint": {
                "path": "/discounts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "starting_after"
                }
            }
        },
        {
            "name": "waitlists",
            "endpoint": {
                "path": "/event_series/{event_series_id}/waitlist_signups",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "starting_after"
                }
            }
        },
        {
            "name": "events_series",
            "endpoint": {
                "path": "/event_series",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "starting_after"
                }
            }
        },
        {
            "name": "issued_tickets",
            "endpoint": {
                "path": "/issued_tickets",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "starting_after"
                }
            }
        },
        {
            "name": "vouchers_codes",
            "endpoint": {
                "path": "/vouchers/{voucher_id}/codes",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "starting_after"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Username**: API key (stored securely in `dlt.secrets["api_key"]`)

# Resources

Each resource corresponds to a specific endpoint in the API, with the following configurations:

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized paths for accessing resources.
- **HTTP Method**: GET (default for all resources).
- **Pagination Strategy**: Cursor-based pagination using `starting_after` as the cursor parameter.
- **Data Selector**: Extracts data from the `data` field in the response.

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP errors such as 429 (Too Many Requests) and 500-series server errors.
- **Backoff Strategy**: Exponential backoff for retries.

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the API's data structure. Each schema includes:

- **Fields**: Defined with types and nullability.
- **Primary Key**: `id` field used as the primary key for deduplication and merging.
- **Additional Properties**: Allowed to accommodate any extra fields returned by the API.