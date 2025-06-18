# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and schema. The API requires authentication via an API key, and supports pagination and error handling strategies.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "x-api-key",
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
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [],
                    "max_retries": 3,
                    "backoff_factor": 15
                }
            }
        },
        {
            "name": "email_accounts",
            "endpoint": {
                "path": "/v1/emailAccounts",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [],
                    "max_retries": 3,
                    "backoff_factor": 15
                }
            }
        },
        {
            "name": "people",
            "endpoint": {
                "path": "/v1/people",
                "data_selector": "people",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 1000
                },
                "error_handler": {
                    "retry_on_status_codes": [],
                    "max_retries": 3,
                    "backoff_factor": 15
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/v1/templates",
                "data_selector": "userTemplates",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [],
                    "max_retries": 3,
                    "backoff_factor": 15
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header with the name `x-api-key`.

## Resources

### Campaigns

- **Endpoint Path**: `/v1/campaigns`
- **Pagination**: Single page
- **Data Selector**: `data`
- **Error Handling**: Retries with a constant backoff of 15 seconds

### Email Accounts

- **Endpoint Path**: `/v1/emailAccounts`
- **Pagination**: Single page
- **Data Selector**: `data`
- **Error Handling**: Retries with a constant backoff of 15 seconds

### People

- **Endpoint Path**: `/v1/people`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `people`
- **Error Handling**: Retries with a constant backoff of 15 seconds

### Templates

- **Endpoint Path**: `/v1/templates`
- **Pagination**: Single page
- **Data Selector**: `userTemplates`
- **Error Handling**: Retries with a constant backoff of 15 seconds

## Error Handling

- **Retry Logic**: The API will retry requests with a constant backoff strategy of 15 seconds.
- **Max Retries**: 3 attempts will be made for each request.
- **Backoff Factor**: 15 seconds between retries.

## Schema

Each resource has a defined schema with fields, types, and nullability. The schemas are generalized and do not include any proprietary field names. Here are some example fields:

- **Campaigns**: `id`, `name`, `created`, `bouncesCount`
- **Email Accounts**: `id`, `emailAddress`, `senderName`
- **People**: `id`, `firstName`, `lastName`, `email`
- **Templates**: `id`, `name`, `subject`, `body`

All fields are nullable and support various data types such as `string`, `integer`, and `array`.