# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization from two primary resources: links and clicks. The API uses cursor-based pagination and requires API key authentication.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
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
            "name": "links",
            "endpoint": {
                "path": "/v1/links",
                "data_selector": "links",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        },
        {
            "name": "clicks",
            "endpoint": {
                "path": "/v1/domain/{domain_id}/link_clicks",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Header Name**: Authorization
- **API Key Location**: Header
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.

## Resources

### Links Resource

- **Resource Name**: links
- **Endpoint Path**: `/v1/links`
- **HTTP Method**: GET
- **Data Selector**: `links`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `pageToken`
  - **Cursor Path**: `nextPageToken`

### Clicks Resource

- **Resource Name**: clicks
- **Endpoint Path**: `/v1/domain/{domain_id}/link_clicks`
- **HTTP Method**: GET
- **Data Selector**: `data`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `pageToken`
  - **Cursor Path**: `nextPageToken`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Links Schema

- **Primary Key**: `id`
- **Fields**:
  - `lcpath`: Nullable string
  - `passwordContact`: Nullable boolean
  - `hasPassword`: Nullable boolean
  - `OwnerId`: Nullable integer
  - `id`: Nullable string
  - `path`: Nullable string
  - `title`: Nullable string
  - `icon`: Nullable string
  - `archived`: Nullable boolean
  - `originalURL`: String
  - `iphoneURL`: Nullable string
  - `androidURL`: Nullable string
  - `password`: Nullable string
  - `utmSource`: Nullable string
  - `utmMedium`: Nullable string
  - `utmCampaign`: Nullable string
  - `utmCampaignId`: Nullable string
  - `utmTerm`: Nullable string
  - `utmContent`: Nullable string
  - `splitURL`: Nullable string
  - `splitPercent`: Nullable string
  - `expiresAt`: Nullable string
  - `expiredURL`: Nullable string
  - `redirectType`: Nullable string
  - `clicksLimit`: Nullable string
  - `cloaking`: Nullable boolean
  - `source`: Nullable string
  - `integrationGA`: Nullable string
  - `integrationFB`: Nullable string
  - `integrationAdroll`: Nullable string
  - `integrationGTM`: Nullable string
  - `AutodeletedAt`: Nullable string, date-time format
  - `createdAt`: String, date-time format
  - `updatedAt`: String, date-time format
  - `DomainId`: Integer
  - `Owner`: Nullable object with properties `id`, `name`, `email`, `photoURL`
  - `tags`: Nullable array of strings
  - `secureShortURL`: Nullable string
  - `idString`: String
  - `shortURL`: String
  - `User`: Nullable object with properties `id`, `name`, `email`, `photoURL`

### Clicks Schema

- **Primary Key**: `dt`
- **Fields**:
  - `host`: Nullable string
  - `path`: Nullable string
  - `method`: Nullable string
  - `url`: Nullable string
  - `dt`: Nullable string, date-time format
  - `st`: Nullable integer
  - `ip`: Nullable string
  - `proto`: Nullable string
  - `ref`: Nullable string
  - `ua`: Nullable string
  - `human`: Nullable boolean
  - `browser`: Nullable string
  - `browser_version`: Nullable string
  - `country`: Nullable string
  - `city`: Nullable string
  - `social`: Nullable string
  - `refhost`: Nullable string
  - `os`: Nullable string
  - `utm_source`: Nullable string
  - `utm_medium`: Nullable string
  - `utm_campaign`: Nullable string
  - `goal_completed`: Nullable string
  - `ab_path`: Nullable string
  - `lcpath`: Nullable string

This configuration provides a comprehensive setup for integrating with the API, ensuring data synchronization and error handling are managed effectively.