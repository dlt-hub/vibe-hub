# Overview

This document provides a configuration guide for integrating with a third-party document management API using dltHub's REST API source format. The integration allows users to extract data such as documents, templates, and related metadata, and integrate it into various data warehouses or databases. The API supports multiple resources, each with its own endpoint and configuration.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
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
            "name": "documents",
            "endpoint": {
                "path": "/v1/documents",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "count",
                    "limit": 100
                }
            }
        },
        {
            "name": "document_attachment",
            "endpoint": {
                "path": "/v1/documents/{document_id}/attachments",
                "data_selector": "results"
            }
        },
        {
            "name": "document_field",
            "endpoint": {
                "path": "/v1/documents/{document_id}/fields",
                "data_selector": "fields"
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/v1/templates",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "count",
                    "limit": 100
                }
            }
        },
        {
            "name": "forms",
            "endpoint": {
                "path": "/v1/forms",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "count",
                    "limit": 100
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "results"
            }
        },
        {
            "name": "members",
            "endpoint": {
                "path": "/v1/members",
                "data_selector": "results"
            }
        },
        {
            "name": "api_logs",
            "endpoint": {
                "path": "/v1/logs",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "count",
                    "limit": 100
                }
            }
        },
        {
            "name": "document_folders",
            "endpoint": {
                "path": "/v1/documents/folders",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "count",
                    "limit": 100
                }
            }
        },
        {
            "name": "template_folders",
            "endpoint": {
                "path": "/v1/templates/folders",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "count",
                    "limit": 100
                }
            }
        },
        {
            "name": "workspaces",
            "endpoint": {
                "path": "/v1/workspaces",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "count",
                    "limit": 100
                }
            }
        },
        {
            "name": "webhook_subscriptions",
            "endpoint": {
                "path": "/v1/webhook-subscriptions",
                "data_selector": "items"
            }
        },
        {
            "name": "webhook_events",
            "endpoint": {
                "path": "/v1/webhook-events",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "count",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header under the `Authorization` field.

# Resources

Each resource corresponds to a specific endpoint and data type. Below are the details for each resource:

- **Documents**
  - **Endpoint Path**: `/v1/documents`
  - **Data Selector**: `results`
  - **Pagination**: Page number with `page` and `count` parameters, limit of 100 per page.

- **Document Attachment**
  - **Endpoint Path**: `/v1/documents/{document_id}/attachments`
  - **Data Selector**: `results`

- **Document Field**
  - **Endpoint Path**: `/v1/documents/{document_id}/fields`
  - **Data Selector**: `fields`

- **Templates**
  - **Endpoint Path**: `/v1/templates`
  - **Data Selector**: `results`
  - **Pagination**: Page number with `page` and `count` parameters, limit of 100 per page.

- **Forms**
  - **Endpoint Path**: `/v1/forms`
  - **Data Selector**: `results`
  - **Pagination**: Page number with `page` and `count` parameters, limit of 100 per page.

- **Contacts**
  - **Endpoint Path**: `/v1/contacts`
  - **Data Selector**: `results`

- **Members**
  - **Endpoint Path**: `/v1/members`
  - **Data Selector**: `results`

- **API Logs**
  - **Endpoint Path**: `/v1/logs`
  - **Data Selector**: `results`
  - **Pagination**: Page number with `page` and `count` parameters, limit of 100 per page.

- **Document Folders**
  - **Endpoint Path**: `/v1/documents/folders`
  - **Data Selector**: `results`
  - **Pagination**: Page number with `page` and `count` parameters, limit of 100 per page.

- **Template Folders**
  - **Endpoint Path**: `/v1/templates/folders`
  - **Data Selector**: `results`
  - **Pagination**: Page number with `page` and `count` parameters, limit of 100 per page.

- **Workspaces**
  - **Endpoint Path**: `/v1/workspaces`
  - **Data Selector**: `results`
  - **Pagination**: Page number with `page` and `count` parameters, limit of 100 per page.

- **Webhook Subscriptions**
  - **Endpoint Path**: `/v1/webhook-subscriptions`
  - **Data Selector**: `items`

- **Webhook Events**
  - **Endpoint Path**: `/v1/webhook-events`
  - **Data Selector**: `items`
  - **Pagination**: Page number with `page` and `count` parameters, limit of 100 per page.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 retries with exponential backoff.
- **Backoff Factor**: 2

# Schema

Each resource has a defined schema with fields, types, and nullability. Below are examples of schema fields for some resources:

- **Documents**
  - `id`: string, required
  - `date_modified`: string, required
  - `name`: string or null
  - `status`: string or null

- **Document Attachment**
  - `uuid`: string, required
  - `name`: string or null
  - `date_created`: string or null

- **Templates**
  - `id`: string or null
  - `name`: string or null
  - `date_modified`: string or null

- **Forms**
  - `id`: string, required
  - `date_modified`: string, required
  - `name`: string or null

- **Contacts**
  - `id`: string, required
  - `first_name`: string or null
  - `last_name`: string or null

- **Members**
  - `user_id`: string, required
  - `date_modified`: string, required
  - `email`: string or null

This configuration guide provides a comprehensive overview of how to set up and use the API integration with dltHub's REST API source format, ensuring a seamless data extraction and integration process.