# Overview

This document provides a configuration guide for integrating with a third-party financial data API using dltHub's REST API source format. The integration allows for the retrieval of stock price data, including historical and real-time information, through a RESTful API. The API supports various data intervals and ranges, enabling detailed financial analysis.

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
        "primary_key": "symbol",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "price_data",
            "endpoint": {
                "path": "/v1/finance/chart",
                "params": {
                    "range": "{{ config['range'] }}",
                    "symbol": "{{ config['tickers'].split(',')[next_page_token['next_page_token'] or 0].strip() }}",
                    "interval": "{{ config['interval'] }}"
                },
                "data_selector": "chart.result",
                "paginator": {
                    "type": "page_number",
                    "start_from_page": 0
                },
                "error_handler": {
                    "retry_on_status_codes": [403, 429, 500, 502, 503, 504],
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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `price_data`
- **Endpoint Path**: `/v1/finance/chart`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `page_number` with a starting page of 0
- **Data Selector**: `chart.result`

## Error Handling

- **Retry Logic**: The integration will retry requests on receiving HTTP status codes 403, 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts
- **Backoff Factor**: Exponential backoff with a factor of 2

## Schema

The schema for the `price_data` resource includes the following fields:

- **chart**: Contains the price data for the stock.
  - **result**: Array of objects with stock price information.
    - **indicators**: Technical indicators for the stock price.
      - **quote**: Array of objects with actual stock price data.
        - **close**: Array of closing prices.
        - **high**: Array of highest prices during the trading period.
        - **low**: Array of lowest prices during the trading period.
        - **open**: Array of opening prices.
        - **volume**: Array of total trading volumes.
    - **meta**: Metadata related to the stock price.
      - **chartPreviousClose**: Closing price from the previous trading day.
      - **currency**: Currency of the prices.
      - **currentTradingPeriod**: Information about different trading periods.
      - **dataGranularity**: Granularity of the data intervals.
      - **exchangeName**: Name of the stock exchange.
      - **exchangeTimezoneName**: Timezone of the stock exchange.
      - **firstTradeDate**: Date of the stock's first trade.
      - **gmtoffset**: GMT Offset for the trading data.
      - **instrumentType**: Type of instrument (e.g., stock, ETF).
      - **previousClose**: Closing price from the previous trading day.
      - **priceHint**: Decimal precision for the price data.
      - **range**: Price range during a specific time period.
      - **regularMarketPrice**: Price in the regular market session.
      - **regularMarketTime**: Time of the last price update.
      - **scale**: Numerical scale factor for prices.
      - **symbol**: Symbol or ticker of the stock.
      - **timezone**: Timezone of the trading data.
      - **tradingPeriods**: Information about different trading periods.
      - **validRanges**: Valid trading ranges for the stock.
    - **timestamp**: Array of timestamps for the price data.

This configuration provides a comprehensive setup for accessing and processing financial data from a third-party API using dltHub's REST API source.