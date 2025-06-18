# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization for various resources, including labels, memberships, and transactions. The API uses a cursor-based pagination strategy and requires API key authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "labels",
            "endpoint": {
                "path": "/v1/labels",
                "data_selector": "labels",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "current_page",
                    "cursor_path": "meta.next_page"
                }
            }
        },
        {
            "name": "memberships",
            "endpoint": {
                "path": "/v1/memberships",
                "data_selector": "memberships",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "current_page",
                    "cursor_path": "meta.next_page"
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v1/transactions",
                "data_selector": "transactions",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "current_page",
                    "cursor_path": "meta.next_page"
                },
                "params": {
                    "iban": dlt.config["iban"],
                    "settled_at_from": dlt.config["start_date"]
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: Authorization
- **API Key**: Stored securely in `dlt.secrets["api_key"]`

# Resources

## Labels
- **Endpoint Path**: `/v1/labels`
- **Data Selector**: `labels`
- **Pagination**: Cursor-based
  - **Cursor Parameter**: `current_page`
  - **Cursor Path**: `meta.next_page`

## Memberships
- **Endpoint Path**: `/v1/memberships`
- **Data Selector**: `memberships`
- **Pagination**: Cursor-based
  - **Cursor Parameter**: `current_page`
  - **Cursor Path**: `meta.next_page`

## Transactions
- **Endpoint Path**: `/v1/transactions`
- **Data Selector**: `transactions`
- **Pagination**: Cursor-based
  - **Cursor Parameter**: `current_page`
  - **Cursor Path**: `meta.next_page`
- **Additional Parameters**:
  - `iban`: Configured via `dlt.config["iban"]`
  - `settled_at_from`: Configured via `dlt.config["start_date"]`

# Error Handling

- **Retry Logic**: Implemented with exponential backoff
- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

## Labels
- **Fields**:
  - `id`: string
  - `name`: nullable string
  - `parent_id`: nullable string

## Memberships
- **Fields**:
  - `birth_country`: nullable string
  - `birthdate`: nullable string
  - `first_name`: nullable string
  - `id`: string
  - `last_name`: nullable string
  - `nationality`: nullable string
  - `residence_country`: nullable string
  - `role`: nullable string
  - `ubo`: nullable boolean

## Transactions
- **Fields**:
  - `amount`: nullable number
  - `amount_cents`: nullable integer
  - `attachment_ids`: nullable array of strings
  - `attachment_lost`: nullable boolean
  - `attachment_required`: nullable boolean
  - `card_last_digits`: nullable string
  - `category`: nullable string
  - `currency`: nullable string
  - `emitted_at`: nullable string
  - `id`: nullable string
  - `initiator_id`: nullable string
  - `label`: nullable string
  - `label_ids`: nullable array of strings
  - `local_amount`: nullable number
  - `local_amount_cents`: nullable integer
  - `local_currency`: nullable string
  - `note`: nullable string
  - `operation_type`: nullable string
  - `reference`: nullable string
  - `settled_at`: nullable string
  - `settled_balance`: nullable number
  - `settled_balance_cents`: nullable integer
  - `side`: nullable string
  - `status`: nullable string
  - `subject_type`: nullable string
  - `transaction_id`: nullable string
  - `transfer`: nullable object
  - `updated_at`: nullable string
  - `vat_amount`: nullable number
  - `vat_amount_cents`: nullable number
  - `vat_rate`: nullable number

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format.