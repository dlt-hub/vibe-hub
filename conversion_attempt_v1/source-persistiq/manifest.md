# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization for user, lead, and campaign resources, utilizing a cursor-based pagination strategy for efficient data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "api_key",
            "name": "api_key",
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
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "users",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_page"
                }
            }
        },
        {
            "name": "leads",
            "endpoint": {
                "path": "/leads",
                "data_selector": "leads",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_page"
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/campaigns",
                "data_selector": "campaigns",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Configuration**: The API key is stored securely in `dlt.secrets` and is included in the request headers for authentication.

## Resources

### Users

- **Endpoint Path**: `/users`
- **Data Selector**: `users`
- **Primary Key**: `id`
- **Pagination Strategy**: Cursor-based, using `next_page` as the cursor parameter.

### Leads

- **Endpoint Path**: `/leads`
- **Data Selector**: `leads`
- **Primary Key**: `id`
- **Pagination Strategy**: Cursor-based, using `next_page` as the cursor parameter.

### Campaigns

- **Endpoint Path**: `/campaigns`
- **Data Selector**: `campaigns`
- **Primary Key**: `id`
- **Pagination Strategy**: Cursor-based, using `next_page` as the cursor parameter.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts
- **Backoff Factor**: Exponential backoff with a factor of 2

## Schema

### Users

- **Fields**:
  - `activated`: Nullable boolean
  - `default_mailbox_id`: Nullable string
  - `email`: String, email format
  - `id`: String
  - `name`: Nullable string
  - `salesforce_id`: Nullable string

### Leads

- **Fields**:
  - `bounced`: Nullable boolean
  - `creator_id`: Nullable string
  - `data`: Object containing various nullable string fields such as `address`, `city`, `company_name`, etc.
  - `id`: String
  - `last_sent_at`: Nullable string
  - `optedout`: Nullable boolean
  - `owner_id`: String
  - `replied_count`: Nullable integer
  - `sent_count`: Nullable integer
  - `status`: Nullable string

### Campaigns

- **Fields**:
  - `creator`: Nullable object with fields `email`, `id`, `name`
  - `id`: String
  - `name`: Nullable string
  - `stats`: Nullable object with integer fields such as `prospects_bounced`, `prospects_contacted`, etc.

This configuration provides a comprehensive setup for integrating with a third-party analytics API using dltHub's REST API source, ensuring efficient data synchronization and error handling.