# Overview

This document provides a configuration example for integrating with a third-party marketing API using dltHub's REST API source. The integration allows for seamless data extraction from the API into your data processing pipelines. The API supports multiple resources, each with its own endpoint and pagination strategy.

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
            "name": "affiliates",
            "endpoint": {
                "path": "/v1/affiliates",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "links.next",
                    "stop_condition": "not links.next"
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
                    "cursor_path": "links.next",
                    "stop_condition": "not links.next"
                }
            }
        },
        {
            "name": "referrals",
            "endpoint": {
                "path": "/v1/referrals",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "links.next",
                    "stop_condition": "not links.next"
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

### Affiliates

- **Endpoint Path**: `/v1/affiliates`
- **Pagination Strategy**: Cursor-based
  - **Cursor Path**: `links.next`
  - **Stop Condition**: `not links.next`
- **Data Selector**: `data`

### Coupons

- **Endpoint Path**: `/v1/coupons`
- **Pagination Strategy**: Cursor-based
  - **Cursor Path**: `links.next`
  - **Stop Condition**: `not links.next`
- **Data Selector**: `data`

### Referrals

- **Endpoint Path**: `/v1/referrals`
- **Pagination Strategy**: Cursor-based
  - **Cursor Path**: `links.next`
  - **Stop Condition**: `not links.next`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes with exponential backoff.
- **Known HTTP Response Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Affiliates

- **Fields**:
  - `affiliate_link`: string or null
  - `affiliate_setting`: object or null
  - `coupons`: array or null
  - `created_at_timestamp`: number or null
  - `email`: string or null
  - `email_verified`: number or null
  - `first_name`: string or null
  - `id`: number (required)
  - `last_name`: string or null
  - `parent_email`: string or null
  - `parent_id`: number or null
  - `program_id`: number or null
  - `program_name`: string or null
  - `status`: number or null

### Coupons

- **Fields**:
  - `description`: string or null
  - `affiliate_id`: number or null
  - `coupon`: string or null
  - `created_timestamp`: number or null
  - `id`: number (required)

### Referrals

- **Fields**:
  - `affiliate`: object or null
    - `email`: string or null
    - `first_name`: string or null
    - `id`: number or null
    - `last_name`: string or null
  - `affiliate_id`: number or null
  - `commission`: string or null
  - `commission_adjustment`: string or null
  - `created_at`: number (required)
  - `id`: number (required)
  - `status`: string or null
  - `total_sales`: string or null
  - `tracking_type`: string or null

This configuration provides a clean and generalized setup for integrating with a third-party marketing API using dltHub's REST API source, ensuring a vendor-neutral approach.