# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the retrieval of dataset collections and individual datasets, as well as item collections from a specified dataset. The API supports pagination and requires authentication via an API token.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2/",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "dataset_collection",
            "endpoint": {
                "path": "/datasets",
                "data_selector": ["data", "items"],
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "dataset",
            "endpoint": {
                "path": "/datasets/{dataset_id}",
                "data_selector": ["data"],
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "item_collection_website_content_crawler",
            "endpoint": {
                "path": "/datasets/{dataset_id}/items",
                "data_selector": [],
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "item_collection",
            "endpoint": {
                "path": "/datasets/{dataset_id}/items",
                "data_selector": [],
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Use the `api_token` stored in `dlt.secrets`.

## Resources

### Dataset Collection

- **Resource Name**: dataset_collection
- **Endpoint Path**: `/datasets`
- **Pagination Strategy**: Offset
  - **Limit**: 50
  - **Offset Parameter**: `offset`
- **Data Selector**: `["data", "items"]`

### Dataset

- **Resource Name**: dataset
- **Endpoint Path**: `/datasets/{dataset_id}`
- **Pagination Strategy**: Offset
  - **Limit**: 50
  - **Offset Parameter**: `offset`
- **Data Selector**: `["data"]`

### Item Collection - Website Content Crawler

- **Resource Name**: item_collection_website_content_crawler
- **Endpoint Path**: `/datasets/{dataset_id}/items`
- **Pagination Strategy**: Offset
  - **Limit**: 50
  - **Offset Parameter**: `offset`
- **Data Selector**: `[]`

### Item Collection

- **Resource Name**: item_collection
- **Endpoint Path**: `/datasets/{dataset_id}/items`
- **Pagination Strategy**: Offset
  - **Limit**: 50
  - **Offset Parameter**: `offset`
- **Data Selector**: `[]`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource includes fields such as `id`, `name`, `userId`, `createdAt`, `modifiedAt`, `accessedAt`, `itemCount`, `username`, `stats`, `schema`, `cleanItemCount`, `actId`, `actRunId`, `title`, and `fields`. These fields are generalized and do not include any proprietary or branded terms.