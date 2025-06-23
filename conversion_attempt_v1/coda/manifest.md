# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as documents, categories, permissions, pages, tables, formulas, controls, and rows. The API uses bearer token authentication and supports cursor-based pagination for data retrieval.

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
            "name": "documents",
            "endpoint": {
                "path": "/v1/documents",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageLink"
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/v1/categories",
                "data_selector": "items"
            }
        },
        {
            "name": "permissions",
            "endpoint": {
                "path": "/v1/documents/{doc_id}/permissions",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageLink"
                }
            }
        },
        {
            "name": "pages",
            "endpoint": {
                "path": "/v1/documents/{doc_id}/pages",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageLink"
                }
            }
        },
        {
            "name": "tables",
            "endpoint": {
                "path": "/v1/documents/{doc_id}/tables",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageLink"
                }
            }
        },
        {
            "name": "formulas",
            "endpoint": {
                "path": "/v1/documents/{doc_id}/formulas",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageLink"
                }
            }
        },
        {
            "name": "controls",
            "endpoint": {
                "path": "/v1/documents/{doc_id}/controls",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageLink"
                }
            }
        },
        {
            "name": "rows",
            "endpoint": {
                "path": "/v1/documents/{doc_id}/tables/{table_id}/rows",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageLink"
                },
                "error_handler": {
                    "retry_on_status_codes": [404],
                    "max_retries": 3,
                    "backoff_factor": 2
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

- **Documents**: `/v1/documents`
  - Primary Key: `id`
  - Pagination: Cursor-based using `nextPageLink`
  - Data Selector: `items`

- **Categories**: `/v1/categories`
  - Primary Key: `name`
  - Data Selector: `items`

- **Permissions**: `/v1/documents/{doc_id}/permissions`
  - Primary Key: `id`
  - Pagination: Cursor-based using `nextPageLink`
  - Data Selector: `items`

- **Pages**: `/v1/documents/{doc_id}/pages`
  - Primary Key: `id`
  - Pagination: Cursor-based using `nextPageLink`
  - Data Selector: `items`

- **Tables**: `/v1/documents/{doc_id}/tables`
  - Primary Key: `id`
  - Pagination: Cursor-based using `nextPageLink`
  - Data Selector: `items`

- **Formulas**: `/v1/documents/{doc_id}/formulas`
  - Primary Key: `id`
  - Pagination: Cursor-based using `nextPageLink`
  - Data Selector: `items`

- **Controls**: `/v1/documents/{doc_id}/controls`
  - Primary Key: `id`
  - Pagination: Cursor-based using `nextPageLink`
  - Data Selector: `items`

- **Rows**: `/v1/documents/{doc_id}/tables/{table_id}/rows`
  - Primary Key: `id`
  - Pagination: Cursor-based using `nextPageLink`
  - Data Selector: `items`
  - Error Handling: Retries on 404 status code with exponential backoff

# Error Handling

- **Retry Logic**: 
  - Retries on HTTP status code 404
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Documents**: Includes fields like `type`, `browserLink`, `createdAt`, `docSize`, `folder`, `id`, `name`, `owner`, `published`, `sourceDoc`, `updatedAt`, `workspace`.
- **Categories**: Includes field `name`.
- **Permissions**: Includes fields like `access`, `id`, `principal`.
- **Pages**: Includes fields like `type`, `authors`, `browserLink`, `children`, `contentType`, `createdAt`, `createdBy`, `href`, `icon`, `id`, `image`, `name`, `parent`, `subtitle`, `updatedAt`, `updatedBy`.
- **Tables**: Includes fields like `type`, `browserLink`, `href`, `id`, `name`, `parent`, `tableType`.
- **Formulas**: Includes fields like `type`, `href`, `id`, `name`, `parent`.
- **Controls**: Includes fields like `type`, `href`, `id`, `name`, `parent`.
- **Rows**: Includes fields like `type`, `browserLink`, `createdAt`, `href`, `id`, `index`, `name`, `updatedAt`, `values`.