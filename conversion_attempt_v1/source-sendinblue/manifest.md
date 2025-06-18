# Overview

This document provides a configuration guide for integrating with a third-party email marketing API using dltHub's REST API source format. The integration supports retrieving data from various resources such as campaigns, templates, and contacts, with pagination and authentication mechanisms.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3",
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
            "name": "campaigns",
            "endpoint": {
                "path": "/emailCampaigns",
                "data_selector": "campaigns",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/smtp/templates",
                "data_selector": "templates",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "contacts",
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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the request header.

## Resources

### Campaigns

- **Endpoint Path**: `/emailCampaigns`
- **Data Selector**: `campaigns`
- **Pagination**: Offset-based with `limit` of 100 and `offset` parameter.

### Templates

- **Endpoint Path**: `/smtp/templates`
- **Data Selector**: `templates`
- **Pagination**: Offset-based with `limit` of 100 and `offset` parameter.

### Contacts

- **Endpoint Path**: `/contacts`
- **Data Selector**: `contacts`
- **Pagination**: Offset-based with `limit` of 100 and `offset` parameter.
- **Incremental Sync**: Uses `modifiedAt` as the cursor field to fetch records modified since the last sync.

## Error Handling

- **Retry Logic**: Implement retry mechanisms for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

### Campaigns

- **Fields**: Includes `type`, `abTesting`, `createdAt`, `footer`, `header`, `htmlContent`, `id`, `inlineImageActivation`, `mirrorActive`, `modifiedAt`, `name`, `previewText`, `recipients`, `replyTo`, `scheduledAt`, `sendAtBestTime`, `sender`, `shareLink`, `statistics`, `status`, `subject`, `tag`, `testSent`, `toField`.
- **Types**: Various types including `string`, `boolean`, `integer`, `object`, and `null`.

### Templates

- **Fields**: Includes `type`, `createdAt`, `htmlContent`, `id`, `isActive`, `modifiedAt`, `name`, `replyTo`, `sender`, `subject`, `tag`, `testSent`, `toField`.
- **Types**: Various types including `string`, `boolean`, `integer`, `object`, and `null`.

### Contacts

- **Fields**: Includes `attributes`, `createdAt`, `email`, `emailBlacklisted`, `id`, `listIds`, `modifiedAt`, `smsBlacklisted`.
- **Types**: Various types including `string`, `boolean`, `integer`, `array`, `object`, and `null`.

This configuration provides a comprehensive setup for accessing and synchronizing data from the specified API endpoints using dltHub's REST API source framework.