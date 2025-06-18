# Overview

This document provides a configuration guide for integrating with a third-party API using dltHub's REST API source. The integration allows for seamless data retrieval and synchronization, leveraging a generic API to access various resources efficiently. This setup ensures smooth workflows and real-time data accessibility.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1.0",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://auth.example.com/oauth2/v2.0/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "resource_lists",
            "endpoint": {
                "path": "/sites/{site_id}/lists",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink"
                }
            }
        },
        {
            "name": "resource_listcontenttypes",
            "endpoint": {
                "path": "/sites/{site_id}/lists/{list_id}/contentTypes",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink"
                }
            }
        },
        {
            "name": "resource_listitems",
            "endpoint": {
                "path": "/sites/{site_id}/lists/{list_id}/items/{item_id}",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink"
                }
            }
        },
        {
            "name": "resource_items",
            "endpoint": {
                "path": "/sites/{site_id}/lists/{list_id}/items",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink"
                }
            }
        },
        {
            "name": "resource_columnvalues",
            "endpoint": {
                "path": "/sites/{site_id}/lists/{list_id}/items/{item_id}",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Client ID**: `dlt.secrets["client_id"]`
- **Client Secret**: `dlt.secrets["client_secret"]`
- **Token URL**: `https://auth.example.com/oauth2/v2.0/token`

## Resources

### Resource: Lists

- **Endpoint Path**: `/sites/{site_id}/lists`
- **HTTP Method**: GET
- **Data Selector**: `value`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `$skiptoken`
  - **Cursor Path**: `@odata.nextLink`

### Resource: List Content Types

- **Endpoint Path**: `/sites/{site_id}/lists/{list_id}/contentTypes`
- **HTTP Method**: GET
- **Data Selector**: `value`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `$skiptoken`
  - **Cursor Path**: `@odata.nextLink`

### Resource: List Items

- **Endpoint Path**: `/sites/{site_id}/lists/{list_id}/items/{item_id}`
- **HTTP Method**: GET
- **Data Selector**: `value`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `$skiptoken`
  - **Cursor Path**: `@odata.nextLink`

### Resource: Items

- **Endpoint Path**: `/sites/{site_id}/lists/{list_id}/items`
- **HTTP Method**: GET
- **Data Selector**: `value`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `$skiptoken`
  - **Cursor Path**: `@odata.nextLink`

### Resource: Column Values

- **Endpoint Path**: `/sites/{site_id}/lists/{list_id}/items/{item_id}`
- **HTTP Method**: GET
- **Data Selector**: `value`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `$skiptoken`
  - **Cursor Path**: `@odata.nextLink`

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes
- **Backoff Strategy**: Exponential backoff with a maximum number of retries
- **HTTP Status Codes for Retry**: 429, 500, 502, 503, 504

## Schema

- **Fields**: Use neutral field names such as `id`, `createdDateTime`, `lastModifiedDateTime`, etc.
- **Types**: String, object, boolean, number
- **Nullability**: Fields can be nullable

This configuration ensures a clean and efficient integration with a third-party API, abstracting away any vendor-specific details and focusing on the essential parameters and patterns required for data synchronization.