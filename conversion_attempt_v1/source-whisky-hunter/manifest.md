# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the extraction of data related to auctions and distilleries, providing insights into auction lots, fees, and distillery information.

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
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "auctions_data",
            "endpoint": {
                "path": "/v1/auctions_data",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "auctions_info",
            "endpoint": {
                "path": "/v1/auctions_info",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "distilleries_info",
            "endpoint": {
                "path": "/v1/distilleries_info",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

## Resources

### Auctions Data
- **Endpoint Path**: `/v1/auctions_data`
- **HTTP Method**: GET
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

### Auctions Info
- **Endpoint Path**: `/v1/auctions_info`
- **HTTP Method**: GET
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

### Distilleries Info
- **Endpoint Path**: `/v1/distilleries_info`
- **HTTP Method**: GET
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Auctions Data
- **Fields**:
  - `all_auctions_lots_count`: integer
  - `auction_lots_count`: integer
  - `auction_name`: string
  - `auction_slug`: string
  - `auction_trading_volume`: number
  - `dt`: string
  - `winning_bid_max`: number
  - `winning_bid_mean`: number
  - `winning_bid_min`: number

### Auctions Info
- **Fields**:
  - `base_currency`: string
  - `buyers_fee`: number
  - `listing_fee`: number
  - `name`: string
  - `reserve_fee`: number
  - `sellers_fee`: number
  - `slug`: string
  - `url`: string

### Distilleries Info
- **Fields**:
  - `country`: string
  - `name`: string
  - `slug`: string
  - `whiskybase_rating`: string
  - `whiskybase_votes`: string
  - `whiskybase_whiskies`: string

This configuration provides a clean and vendor-neutral setup for accessing and processing data from a third-party analytics API using dltHub's REST API source format.