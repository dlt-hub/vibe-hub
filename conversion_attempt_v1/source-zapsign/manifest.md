# Overview

This document provides a configuration guide for integrating with a third-party API using dltHub's REST API source format. The API allows access to various resources, including templates, documents, and signers, with support for incremental synchronization based on datetime fields.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "token",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "templates",
            "endpoint": {
                "path": "/v1/templates",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "none"
                }
            }
        },
        {
            "name": "documents",
            "endpoint": {
                "path": "/v1/docs",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "none"
                }
            }
        },
        {
            "name": "signer",
            "endpoint": {
                "path": "/v1/signers/{sign}",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "none"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Use the `api_token` stored in dlt secrets for authentication.

## Resources

### Templates

- **Endpoint Path**: `/v1/templates`
- **Primary Key**: `token`
- **Pagination Strategy**: Cursor-based using the `next` parameter.
- **Data Selector**: `results`

### Documents

- **Endpoint Path**: `/v1/docs`
- **Primary Key**: `token`
- **Pagination Strategy**: Cursor-based using the `next` parameter.
- **Data Selector**: `results`

### Signer

- **Endpoint Path**: `/v1/signers/{sign}`
- **Primary Key**: `token`
- **Pagination Strategy**: Cursor-based using the `next` parameter.
- **Data Selector**: None

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Templates

- **Fields**:
  - `active`: boolean or null
  - `created_at`: string or null
  - `extra_templates`: array or null
  - `folder_path`: string or null
  - `lang`: string or null
  - `last_update_at`: string
  - `name`: string or null
  - `redirect_link`: string or null
  - `template_file`: string or null
  - `template_type`: string or null
  - `token`: string

### Documents

- **Fields**:
  - `created_at`: string or null
  - `created_through`: string or null
  - `deleted`: boolean or null
  - `external_id`: string or null
  - `folder_path`: string or null
  - `last_update_at`: string
  - `name`: string or null
  - `open_id`: number or null
  - `original_file`: string or null
  - `status`: string or null
  - `token`: string

### Signer

- **Fields**:
  - `auth_mode`: string or null
  - `blank_email`: boolean or null
  - `blank_phone`: boolean or null
  - `cnpj`: string or null
  - `cpf`: string or null
  - `document_photo_url`: string or null
  - `document_verse_photo_url`: string or null
  - `email`: string or null
  - `external_id`: string or null
  - `hide_email`: boolean or null
  - `hide_phone`: boolean or null
  - `liveness_photo_url`: string or null
  - `lock_email`: boolean or null
  - `lock_name`: boolean or null
  - `lock_phone`: boolean or null
  - `name`: string or null
  - `phone_country`: string or null
  - `phone_number`: string or null
  - `qualification`: string or null
  - `redirect_link`: string or null
  - `require_document_photo`: boolean or null
  - `require_selfie_photo`: boolean or null
  - `selfie_photo_url`: string or null
  - `selfie_photo_url2`: string or null
  - `selfie_validation_type`: string or null
  - `send_via`: string or null
  - `sign_url`: string or null
  - `status`: string or null
  - `times_viewed`: number or null
  - `token`: string

This configuration provides a clean, vendor-neutral setup for accessing a third-party API using dltHub's REST API source format.