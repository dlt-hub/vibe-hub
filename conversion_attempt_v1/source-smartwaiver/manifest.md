# Overview

This document provides a configuration guide for integrating with a third-party API that manages digital documents and records. The integration allows for automated data extraction related to document templates, signed records, detailed records, and check-ins. The API supports various data retrieval methods, including incremental synchronization based on timestamps.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v4/",
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
            "name": "document_templates",
            "endpoint": {
                "path": "/v1/templates",
                "data_selector": "templates"
            }
        },
        {
            "name": "signed_records",
            "endpoint": {
                "path": "/v1/waivers",
                "data_selector": "waivers",
                "paginator": {
                    "type": "offset",
                    "limit": 300
                }
            }
        },
        {
            "name": "detailed_signed_record",
            "endpoint": {
                "path": "/v1/waivers/{parent_id}",
                "data_selector": "waiver"
            }
        },
        {
            "name": "checkins",
            "endpoint": {
                "path": "/v1/checkins",
                "data_selector": "checkins",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer Token
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

## Document Templates
- **Endpoint Path**: `/v1/templates`
- **Data Selector**: `templates`
- **Primary Key**: `templateId`

## Signed Records
- **Endpoint Path**: `/v1/waivers`
- **Data Selector**: `waivers`
- **Primary Key**: `waiverId`
- **Pagination**: Offset-based with a limit of 300 records per request

## Detailed Signed Record
- **Endpoint Path**: `/v1/waivers/{parent_id}`
- **Data Selector**: `waiver`
- **Primary Key**: `waiverId`

## Check-ins
- **Endpoint Path**: `/v1/checkins`
- **Data Selector**: `checkins`
- **Primary Key**: `waiverId`
- **Pagination**: Offset-based with a limit of 100 records per request

# Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

## Document Templates
- **Fields**: `templateId`, `title`, `publishedOn`, etc.
- **Types**: String, Number, Array, Boolean

## Signed Records
- **Fields**: `waiverId`, `createdOn`, `firstName`, `lastName`, etc.
- **Types**: String, Boolean, Array

## Detailed Signed Record
- **Fields**: `waiverId`, `firstName`, `lastName`, `email`, etc.
- **Types**: String, Boolean, Array, Object

## Check-ins
- **Fields**: `waiverId`, `date`, `firstName`, `lastName`, etc.
- **Types**: String, Number

This configuration provides a comprehensive setup for interacting with the API, ensuring data is retrieved efficiently and securely.