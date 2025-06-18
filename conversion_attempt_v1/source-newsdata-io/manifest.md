# Overview

This document provides a configuration guide for integrating with a third-party news API to retrieve the latest and historical news articles. The API supports pagination and allows filtering by various parameters such as countries, categories, and domains. The integration is designed to handle both real-time and historical data retrieval.

## Configuration Example

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
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "article_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "latest_news",
            "endpoint": {
                "path": "/v1/latest",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page",
                    "cursor_path": "nextPage"
                }
            }
        },
        {
            "name": "historical_news",
            "endpoint": {
                "path": "/v1/archive",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page",
                    "cursor_path": "nextPage"
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

## Resources

### Latest News

- **Resource Name**: `latest_news`
- **Endpoint Path**: `/v1/latest`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Cursor-based pagination using the `page` parameter and `nextPage` cursor path.
- **Data Selector**: The data is extracted from the `results` field.

### Historical News

- **Resource Name**: `historical_news`
- **Endpoint Path**: `/v1/archive`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Cursor-based pagination using the `page` parameter and `nextPage` cursor path.
- **Data Selector**: The data is extracted from the `results` field.
- **Error Handling**: Includes retry logic for HTTP 403 errors with a maximum of 3 retries and a backoff factor of 2.

## Error Handling

- **Retry Logic**: Configured to retry on specific HTTP status codes, such as 403, with exponential backoff.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Latest News

- **Fields**:
  - `article_id`: string (required)
  - `description`: string or null
  - `category`: array of strings or null
  - `content`: string or null
  - `country`: array of strings or null
  - `language`: string or null
  - `pubDate`: string or null
  - `title`: string or null
  - Additional fields are available and can be configured as needed.

### Historical News

- **Fields**: The schema is flexible and can accommodate various properties as defined by the API response.

This configuration provides a robust framework for integrating with a news API, allowing for efficient data retrieval and management. The setup is designed to be flexible and adaptable to different data requirements and API changes.