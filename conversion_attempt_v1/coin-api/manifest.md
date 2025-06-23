# Overview

This document provides a configuration guide for integrating with a third-party financial data API using the dltHub REST API source. The integration allows for the retrieval of historical data related to market prices, trades, and quotes. The API supports both sandbox and production environments, enabling users to test and deploy their data extraction processes effectively.

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
            "name": "historical_market_data",
            "endpoint": {
                "path": "/v1/market_data/history",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "historical_trades",
            "endpoint": {
                "path": "/v1/trades/history",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "historical_quotes",
            "endpoint": {
                "path": "/v1/quotes/history",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored securely in `dlt.secrets`.

## Resources

### Historical Market Data
- **Resource Name**: `historical_market_data`
- **Endpoint Path**: `/v1/market_data/history`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Offset-based with a limit of 100 records per request
- **Data Selector**: `data`

### Historical Trades
- **Resource Name**: `historical_trades`
- **Endpoint Path**: `/v1/trades/history`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Offset-based with a limit of 100 records per request
- **Data Selector**: `data`

### Historical Quotes
- **Resource Name**: `historical_quotes`
- **Endpoint Path**: `/v1/quotes/history`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Offset-based with a limit of 100 records per request
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.

## Schema

### Historical Market Data Schema
- **Fields**:
  - `price_close`: number, nullable
  - `price_high`: number, nullable
  - `price_low`: number, nullable
  - `price_open`: number, nullable
  - `time_close`: string, nullable, format: date-time
  - `time_open`: string, nullable, format: date-time
  - `time_period_end`: string, nullable, format: date-time
  - `time_period_start`: string, nullable, format: date-time
  - `trades_count`: integer, nullable
  - `volume_traded`: number, nullable

### Historical Trades Schema
- **Fields**:
  - `price`: number, nullable
  - `size`: number, nullable
  - `symbol_id`: string, nullable
  - `taker_side`: string, nullable
  - `time_trade`: string, nullable, format: date-time
  - `uuid`: string, nullable

### Historical Quotes Schema
- **Fields**:
  - `ask_price`: number, nullable
  - `ask_size`: number, nullable
  - `bid_price`: number, nullable
  - `bid_size`: number, nullable
  - `symbol_id`: string, nullable
  - `time_quote`: string, nullable, format: date-time

This configuration provides a comprehensive setup for accessing and processing historical financial data through a REST API, ensuring a seamless integration with dltHub's data pipeline capabilities.