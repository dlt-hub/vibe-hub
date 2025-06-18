# Overview

This document provides a configuration guide for integrating with a third-party CRM API using dltHub's REST API source. The integration allows for seamless data extraction and synchronization of customer relationship data, enabling businesses to maintain up-to-date records and leverage insights for improved decision-making and marketing efforts.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["access_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "accounts",
            "endpoint": {
                "path": "/accounts",
                "data_selector": "list.*.record",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "list.*.record",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "opportunities",
            "endpoint": {
                "path": "/opportunities",
                "data_selector": "list.*.record",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "documents",
            "endpoint": {
                "path": "/opportunity_lines",
                "data_selector": "list.*.record",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/campaigns",
                "data_selector": "list.*.record",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "cases",
            "endpoint": {
                "path": "/cases",
                "data_selector": "list.*.record",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/activities",
                "data_selector": "list.*.record",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "opportunity_histories",
            "endpoint": {
                "path": "/opportunityhistories",
                "data_selector": "list.*.record",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "opportunity_lines",
            "endpoint": {
                "path": "/opportunity_lines",
                "data_selector": "list.*.record",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Use the `access_token` stored in `dlt.secrets`.

## Resources

### Accounts
- **Endpoint Path**: `/accounts`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `list.*.record`

### Contacts
- **Endpoint Path**: `/contacts`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `list.*.record`

### Opportunities
- **Endpoint Path**: `/opportunities`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `list.*.record`

### Documents
- **Endpoint Path**: `/opportunity_lines`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `list.*.record`

### Campaigns
- **Endpoint Path**: `/campaigns`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `list.*.record`

### Cases
- **Endpoint Path**: `/cases`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `list.*.record`

### Activities
- **Endpoint Path**: `/activities`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `list.*.record`

### Opportunity Histories
- **Endpoint Path**: `/opportunityhistories`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `list.*.record`

### Opportunity Lines
- **Endpoint Path**: `/opportunity_lines`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `list.*.record`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a schema with fields such as `id`, `createdby`, `modifiedby`, and other relevant fields. All fields are nullable unless specified otherwise. Use neutral field names for consistency.