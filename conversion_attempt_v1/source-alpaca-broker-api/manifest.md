# Overview

This document provides a configuration guide for integrating with a third-party financial API using dltHub's REST API source format. The integration allows for the extraction of various resources such as assets, account activities, and more, using a RESTful approach. The API supports both full and incremental synchronization of data.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "assets",
            "endpoint": {
                "path": "/v1/assets",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "accounts_activities",
            "endpoint": {
                "path": "/v1/accounts/activities",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page_token",
                    "cursor_path": "id"
                }
            }
        },
        {
            "name": "accounts",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "account_documents",
            "endpoint": {
                "path": "/v1/accounts/{acc_id}/documents",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "calendar",
            "endpoint": {
                "path": "/v1/calendar",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "clock",
            "endpoint": {
                "path": "/v1/clock",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "country_info",
            "endpoint": {
                "path": "/v1/country-info",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: API Key ID
  - `password`: API Secret Key

# Resources

### Assets
- **Endpoint Path**: `/v1/assets`
- **Primary Key**: `id`
- **Pagination**: Single page

### Accounts Activities
- **Endpoint Path**: `/v1/accounts/activities`
- **Primary Key**: `id`
- **Pagination**: Cursor-based
  - **Cursor Parameter**: `page_token`
  - **Cursor Path**: `id`

### Accounts
- **Endpoint Path**: `/v1/accounts`
- **Primary Key**: `id`
- **Pagination**: Single page

### Account Documents
- **Endpoint Path**: `/v1/accounts/{acc_id}/documents`
- **Primary Key**: `id`
- **Pagination**: Single page

### Calendar
- **Endpoint Path**: `/v1/calendar`
- **Primary Key**: `uuid`
- **Pagination**: Single page

### Clock
- **Endpoint Path**: `/v1/clock`
- **Primary Key**: `uuid`
- **Pagination**: Single page

### Country Info
- **Endpoint Path**: `/v1/country-info`
- **Primary Key**: `uuid`
- **Pagination**: Single page

# Error Handling

- **Retry Logic**: Implemented for HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a defined schema with fields such as `id`, `date`, `uuid`, etc. The schemas are designed to be flexible and accommodate various data types, ensuring compatibility with the API's responses.