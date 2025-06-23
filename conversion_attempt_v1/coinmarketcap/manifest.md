# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with specific endpoints and pagination strategies. Authentication is handled via an API key, and data is retrieved using GET requests.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "categories",
            "endpoint": {
                "path": "/v1/resource/categories",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000
                }
            }
        },
        {
            "name": "listing",
            "endpoint": {
                "path": "/v1/resource/listings",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000
                }
            }
        },
        {
            "name": "quotes",
            "endpoint": {
                "path": "/v1/resource/quotes",
                "data_selector": "data"
            }
        },
        {
            "name": "fiat",
            "endpoint": {
                "path": "/v1/resource/fiat",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000
                }
            }
        },
        {
            "name": "exchange",
            "endpoint": {
                "path": "/v1/resource/exchange",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Configuration**: The API key is stored securely and injected into the request headers.

## Resources

### Categories
- **Endpoint Path**: `/v1/resource/categories`
- **Pagination**: Offset-based with a limit of 1000 records per request
- **Data Selector**: `data`

### Listing
- **Endpoint Path**: `/v1/resource/listings`
- **Pagination**: Offset-based with a limit of 1000 records per request
- **Data Selector**: `data`

### Quotes
- **Endpoint Path**: `/v1/resource/quotes`
- **Data Selector**: `data`

### Fiat
- **Endpoint Path**: `/v1/resource/fiat`
- **Pagination**: Offset-based with a limit of 1000 records per request
- **Data Selector**: `data`

### Exchange
- **Endpoint Path**: `/v1/resource/exchange`
- **Pagination**: Offset-based with a limit of 1000 records per request
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implemented for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts with exponential backoff.

## Schema

### Categories
- **Fields**: `avg_price_change`, `description`, `id`, `last_updated`, `market_cap`, `market_cap_change`, `name`, `num_tokens`, `title`, `volume`, `volume_change`
- **Types**: Mixed types including `null`, `number`, `string`, `integer`

### Listing
- **Fields**: `circulating_supply`, `cmc_rank`, `date_added`, `id`, `last_updated`, `max_supply`, `name`, `num_market_pairs`, `platform`, `quote`, `self_reported_circulating_supply`, `self_reported_market_cap`, `slug`, `symbol`, `tags`, `total_supply`, `tvl_ratio`
- **Types**: Mixed types including `null`, `number`, `string`, `integer`, `object`, `array`

### Quotes
- **Fields**: Dynamic schema with no predefined properties

### Fiat
- **Fields**: `id`, `name`, `sign`, `symbol`
- **Types**: Mixed types including `null`, `string`, `integer`

### Exchange
- **Fields**: `first_historical_data`, `id`, `is_active`, `last_historical_data`, `name`, `slug`
- **Types**: Mixed types including `null`, `string`, `integer`