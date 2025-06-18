# Overview

This document provides a configuration guide for integrating with a third-party market data API. The API offers access to market news for global exchanges and trading data for US stocks. The integration supports extracting data from various resources, including end-of-day (EOD) data, intraday data, and news feeds.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "api_key",
            "name": "api_token",
            "api_key": dlt.secrets["api_key"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "news_feeds_per_symbols",
            "endpoint": {
                "path": "/news/all",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "eod_data",
            "endpoint": {
                "path": "/data/eod",
                "data_selector": "data"
            }
        },
        {
            "name": "intraday_unadjusted_data",
            "endpoint": {
                "path": "/data/intraday",
                "data_selector": "data"
            }
        },
        {
            "name": "news_feeds_per_industry",
            "endpoint": {
                "path": "/news/all",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of each request.

## Resources

### News Feeds Per Symbols

- **Endpoint Path**: `/news/all`
- **Primary Key**: `uuid`
- **Pagination**: Page number-based with a limit of 50 items per page.
- **Data Selector**: `data`

### EOD Data

- **Endpoint Path**: `/data/eod`
- **Primary Key**: Combination of `date` and `ticker`
- **Data Selector**: `data`

### Intraday Unadjusted Data

- **Endpoint Path**: `/data/intraday`
- **Primary Key**: Combination of `date` and `ticker`
- **Data Selector**: `data`

### News Feeds Per Industry

- **Endpoint Path**: `/news/all`
- **Primary Key**: `uuid`
- **Pagination**: Page number-based with a limit of 50 items per page.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts
- **Backoff Factor**: Exponential backoff with a factor of 2

## Schema

### News Feeds Per Symbols

- **Fields**: Includes `uuid`, `published_at`, `description`, `entities`, `image_url`, `keywords`, `language`, `similar`, `snippet`, `source`, `title`, `url`.
- **Required Fields**: `uuid`, `published_at`

### EOD Data

- **Fields**: Includes `date`, `symbol`, `close`, `high`, `low`, `open`, `volume`.
- **Required Fields**: `date`, `symbol`

### Intraday Unadjusted Data

- **Fields**: Includes `date`, `ticker`, `data` (with subfields `close`, `high`, `is_extended_hours`, `low`, `open`, `volume`).
- **Required Fields**: `date`, `ticker`

### News Feeds Per Industry

- **Fields**: Similar to `news_feeds_per_symbols` with `uuid`, `published_at`, `description`, `entities`, `image_url`, `keywords`, `language`, `similar`, `snippet`, `source`, `title`, `url`.
- **Required Fields**: `uuid`, `published_at`

This configuration allows for a seamless integration with the market data API, providing access to a variety of financial data resources.