# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and schema. The API uses OAuth2 for authentication and supports incremental data synchronization based on datetime fields.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "refresh_token": dlt.secrets["refresh_token"],
            "token_url": "https://api.example.com/auth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "analytics_conversions",
            "endpoint": {
                "path": "/v1/analytics/conversions",
                "data_selector": "conversions",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "analytic_emails",
            "endpoint": {
                "path": "/v1/analytics/emails",
                "data_selector": "emails",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "analytics_funnel",
            "endpoint": {
                "path": "/v1/analytics/funnel",
                "data_selector": "funnel",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "analytics_workflow_emails_statistics",
            "endpoint": {
                "path": "/v1/analytics/workflow_emails",
                "data_selector": "workflow_email_statistics",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "emails",
            "endpoint": {
                "path": "/v1/emails",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 125
                }
            }
        },
        {
            "name": "embeddables",
            "endpoint": {
                "path": "/v1/embeddables",
                "data_selector": [],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 125
                }
            }
        },
        {
            "name": "fields",
            "endpoint": {
                "path": "/v1/contacts/fields",
                "data_selector": "fields",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "landing_pages",
            "endpoint": {
                "path": "/v1/landing_pages",
                "data_selector": [],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 125
                }
            }
        },
        {
            "name": "popups",
            "endpoint": {
                "path": "/v1/popups",
                "data_selector": [],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 125
                }
            }
        },
        {
            "name": "segmentations",
            "endpoint": {
                "path": "/v1/segmentations",
                "data_selector": "segmentations",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 125
                }
            }
        },
        {
            "name": "workflows",
            "endpoint": {
                "path": "/v1/workflows",
                "data_selector": "workflows",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 125
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets**:
  - `client_id`: Your application's client ID.
  - `client_secret`: Your application's client secret.
  - `refresh_token`: Token used to obtain a new access token.
- **Token URL**: `https://api.example.com/auth/token`

## Resources

### Analytics Conversions
- **Endpoint Path**: `/v1/analytics/conversions`
- **Data Selector**: `conversions`
- **Primary Key**: `asset_id`
- **Pagination**: Single page

### Analytic Emails
- **Endpoint Path**: `/v1/analytics/emails`
- **Data Selector**: `emails`
- **Primary Key**: `campaign_id`
- **Pagination**: Single page

### Analytics Funnel
- **Endpoint Path**: `/v1/analytics/funnel`
- **Data Selector**: `funnel`
- **Primary Key**: `reference_day`
- **Pagination**: Single page

### Analytics Workflow Emails Statistics
- **Endpoint Path**: `/v1/analytics/workflow_emails`
- **Data Selector**: `workflow_email_statistics`
- **Primary Key**: `workflow_id`
- **Pagination**: Single page

### Emails
- **Endpoint Path**: `/v1/emails`
- **Data Selector**: `items`
- **Primary Key**: `id`
- **Pagination**: Page number with `page` and `page_size` parameters

### Embeddables
- **Endpoint Path**: `/v1/embeddables`
- **Data Selector**: None
- **Primary Key**: `id`
- **Pagination**: Page number with `page` and `page_size` parameters

### Fields
- **Endpoint Path**: `/v1/contacts/fields`
- **Data Selector**: `fields`
- **Primary Key**: `uuid`
- **Pagination**: Single page

### Landing Pages
- **Endpoint Path**: `/v1/landing_pages`
- **Data Selector**: None
- **Primary Key**: `id`
- **Pagination**: Page number with `page` and `page_size` parameters

### Popups
- **Endpoint Path**: `/v1/popups`
- **Data Selector**: None
- **Primary Key**: `id`
- **Pagination**: Page number with `page` and `page_size` parameters

### Segmentations
- **Endpoint Path**: `/v1/segmentations`
- **Data Selector**: `segmentations`
- **Primary Key**: `id`
- **Pagination**: Page number with `page` and `page_size` parameters

### Workflows
- **Endpoint Path**: `/v1/workflows`
- **Data Selector**: `workflows`
- **Primary Key**: `id`
- **Pagination**: Page number with `page` and `page_size` parameters

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a defined schema with fields, types, and nullability. Field names are generalized to ensure vendor neutrality. For example, `asset_id` is used instead of any branded identifier.