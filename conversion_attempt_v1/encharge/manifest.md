# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for seamless data synchronization between the API and your data warehouse, enabling improved data-driven decision-making and enhanced insights across platforms.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
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
            "name": "schemas",
            "endpoint": {
                "path": "/schemas",
                "data_selector": "objects"
            }
        },
        {
            "name": "peoples",
            "endpoint": {
                "path": "/people/all",
                "data_selector": "people",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "accounts",
            "endpoint": {
                "path": "/accounts",
                "data_selector": []
            }
        },
        {
            "name": "account_tags",
            "endpoint": {
                "path": "/tags-management",
                "data_selector": "tags"
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/segments",
                "data_selector": "segments"
            }
        },
        {
            "name": "fields",
            "endpoint": {
                "path": "/fields",
                "data_selector": "items"
            }
        },
        {
            "name": "segment_people",
            "endpoint": {
                "path": "/segments/{{stream_slice.segment_id}}/people",
                "data_selector": "people",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets` for authentication.
- **Location**: The API key is injected into the request header.

## Resources

### Schemas
- **Endpoint Path**: `/schemas`
- **Data Selector**: `objects`

### Peoples
- **Endpoint Path**: `/people/all`
- **Data Selector**: `people`
- **Pagination Strategy**: Offset pagination with `limit` of 100 and `offset` parameter.

### Accounts
- **Endpoint Path**: `/accounts`
- **Data Selector**: []

### Account Tags
- **Endpoint Path**: `/tags-management`
- **Data Selector**: `tags`

### Segments
- **Endpoint Path**: `/segments`
- **Data Selector**: `segments`

### Fields
- **Endpoint Path**: `/fields`
- **Data Selector**: `items`

### Segment People
- **Endpoint Path**: `/segments/{{stream_slice.segment_id}}/people`
- **Data Selector**: `people`
- **Pagination Strategy**: Offset pagination with `limit` of 100 and `offset` parameter.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with neutral field names and types. For example:

### Peoples Schema
- **ID**: `string`
- **Email**: `string`
- **First Name**: `string`
- **Last Name**: `string`
- **Created At**: `string`
- **Updated At**: `string`

### Accounts Schema
- **Account ID**: `number`
- **Name**: `string`
- **Created At**: `string`

This configuration ensures a clean, vendor-neutral integration with a third-party analytics API, following dltHub's REST API source format.