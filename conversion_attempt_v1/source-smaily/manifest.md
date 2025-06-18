# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as users, segments, campaigns, templates, automations, and A/B tests. The API uses basic authentication and supports pagination for certain resources.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_username"],
            "password": dlt.secrets["api_password"]
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
                "path": "/organizations/users.php",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 250
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/list.php",
                "data_selector": "data"
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/campaign.php",
                "data_selector": "data"
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/templates.php",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 1000
                }
            }
        },
        {
            "name": "automations",
            "endpoint": {
                "path": "/autoresponder.php",
                "data_selector": "data"
            }
        },
        {
            "name": "ab_tests",
            "endpoint": {
                "path": "/split.php",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_username`: The username for API access.
  - `api_password`: The password for API access.

# Resources

### Users
- **Endpoint Path**: `/organizations/users.php`
- **Pagination**: Page number with `page` and `limit` parameters, limit set to 250.
- **Data Selector**: `data`

### Segments
- **Endpoint Path**: `/list.php`
- **Data Selector**: `data`

### Campaigns
- **Endpoint Path**: `/campaign.php`
- **Data Selector**: `data`

### Templates
- **Endpoint Path**: `/templates.php`
- **Pagination**: Page number with `page` and `limit` parameters, limit set to 1000.
- **Data Selector**: `data`

### Automations
- **Endpoint Path**: `/autoresponder.php`
- **Data Selector**: `data`

### A/B Tests
- **Endpoint Path**: `/split.php`
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a defined schema with fields, types, and nullability. The schemas are consistent with the provided YAML configuration and use neutral field names. For example:

### Users Schema
- **Fields**:
  - `email`: string or null
  - `first_name`: string or null
  - `id`: integer (required)
  - `is_owner`: boolean or null
  - `last_name`: string or null
  - `role`: object or null
    - `id`: integer
    - `name`: string or null

### Segments Schema
- **Fields**:
  - `id`: string (required)
  - `name`: string or null
  - `subscribers_count`: integer or null

### Campaigns Schema
- **Fields**:
  - `completed_at`: string or null
  - `created_at`: string or null
  - `id`: string (required)
  - `name`: string or null
  - `status`: string or null
  - `tags`: array or null
  - `template`: object or null
    - `id`: string
    - `name`: string or null
    - `preview_url`: string or null

### Templates Schema
- **Fields**:
  - `type`: string or null
  - `created_at`: string or null
  - `id`: integer (required)
  - `modified_at`: string or null
  - `name`: string or null
  - `preview_url`: string or null

### Automations Schema
- **Fields**:
  - `id`: string (required)
  - `name`: string or null
  - `sections`: array or null
    - `id`: integer
    - `subject`: string or null
    - `template`: object or null
      - `id`: integer
      - `name`: string or null
      - `preview_url`: string or null

### A/B Tests Schema
- **Fields**:
  - `completed_at`: string or null
  - `created_at`: string or null
  - `id`: integer (required)
  - `name`: string or null
  - `sections`: array or null
  - `status`: string or null
  - `tags`: array or null

This configuration ensures a clean and vendor-neutral integration with the third-party analytics API, following the dltHub REST API source format.