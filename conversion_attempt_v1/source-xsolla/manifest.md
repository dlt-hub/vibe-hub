# Overview

This document provides a configuration guide for integrating with a third-party API using dltHub's REST API source. The integration allows for data extraction from various resources such as game catalogs, digital rights management (DRM) information, virtual items, virtual currencies, and more. The API supports offset-based pagination and requires authentication via an API key.

# Configuration Example

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
            "name": "games_catalog",
            "endpoint": {
                "path": "/v2/project/{project_id}/items/game",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "drm",
            "endpoint": {
                "path": "/v2/project/{project_id}/items/game/drm",
                "data_selector": "drm"
            }
        },
        {
            "name": "virtual_items",
            "endpoint": {
                "path": "/v2/project/{project_id}/items/virtual_items",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "virtual_currencies",
            "endpoint": {
                "path": "/v2/project/{project_id}/items/virtual_currency",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "virtual_currency_packages",
            "endpoint": {
                "path": "/v2/project/{project_id}/items/virtual_currency/package",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "bundles",
            "endpoint": {
                "path": "/v2/project/{project_id}/items/bundle",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "reward_chains",
            "endpoint": {
                "path": "/v2/project/{project_id}/user/reward_chain",
                "data_selector": "items",
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

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

- **games_catalog**
  - **Endpoint Path**: `/v2/project/{project_id}/items/game`
  - **Data Selector**: `items`
  - **Pagination**: Offset with limit 50, offset parameter `offset`

- **drm**
  - **Endpoint Path**: `/v2/project/{project_id}/items/game/drm`
  - **Data Selector**: `drm`

- **virtual_items**
  - **Endpoint Path**: `/v2/project/{project_id}/items/virtual_items`
  - **Data Selector**: `items`
  - **Pagination**: Offset with limit 50, offset parameter `offset`

- **virtual_currencies**
  - **Endpoint Path**: `/v2/project/{project_id}/items/virtual_currency`
  - **Data Selector**: `items`
  - **Pagination**: Offset with limit 50, offset parameter `offset`

- **virtual_currency_packages**
  - **Endpoint Path**: `/v2/project/{project_id}/items/virtual_currency/package`
  - **Data Selector**: `items`
  - **Pagination**: Offset with limit 50, offset parameter `offset`

- **bundles**
  - **Endpoint Path**: `/v2/project/{project_id}/items/bundle`
  - **Data Selector**: `items`
  - **Pagination**: Offset with limit 50, offset parameter `offset`

- **reward_chains**
  - **Endpoint Path**: `/v2/project/{project_id}/user/reward_chain`
  - **Data Selector**: `items`
  - **Pagination**: Offset with limit 50, offset parameter `offset`

# Error Handling

- **Retry Logic**: Implement retry on status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **games_catalog**: Includes fields like `type`, `description`, `attributes`, `item_id`, `name`, etc.
- **drm**: Includes fields like `drm_id`, `image`, `name`, `redeem_instruction_link`, `sku`, etc.
- **virtual_items**: Includes fields like `type`, `description`, `attributes`, `item_id`, `name`, etc.
- **virtual_currencies**: Includes fields like `type`, `description`, `attributes`, `item_id`, `name`, etc.
- **virtual_currency_packages**: Includes fields like `type`, `description`, `attributes`, `item_id`, `name`, etc.
- **bundles**: Includes fields like `type`, `description`, `attributes`, `item_id`, `name`, etc.
- **reward_chains**: Includes fields like `description`, `date_end`, `date_start`, `reward_chain_id`, etc.

This configuration provides a comprehensive setup for accessing and extracting data from a third-party API using dltHub's REST API source.