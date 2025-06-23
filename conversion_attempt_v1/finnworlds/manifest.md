# Overview

This document provides a configuration guide for integrating with a third-party financial data API. The API offers access to various financial data streams, including bonds, dividends, stock splits, historical candlestick data, macroeconomic indicators, and more. This integration allows users to fetch and process data related to globally traded financial instruments.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/v1/",
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
            "name": "bonds",
            "endpoint": {
                "path": "/v1/bonds",
                "data_selector": "result.output",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "dividends",
            "endpoint": {
                "path": "/v1/dividends",
                "data_selector": "result.output.dividends",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_splits",
            "endpoint": {
                "path": "/v1/stocksplits",
                "data_selector": "result.output.stocksplits",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "historical_candlestick",
            "endpoint": {
                "path": "/v1/historicalcandlestick",
                "data_selector": "result.output.daily_stock_data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "macro_calendar",
            "endpoint": {
                "path": "/v1/macrocalendar",
                "data_selector": "result.output",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "macro_indicator",
            "endpoint": {
                "path": "/v1/macroindicator",
                "data_selector": "result.output",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "commodities",
            "endpoint": {
                "path": "/v1/commodities",
                "data_selector": "result.output",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "benchmark",
            "endpoint": {
                "path": "/v1/benchmark",
                "data_selector": "result.output",
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
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`
- **Location**: Query parameter

## Resources

### Bonds
- **Endpoint Path**: `/v1/bonds`
- **Data Selector**: `result.output`
- **Primary Key**: `country`, `datetime`, `type`

### Dividends
- **Endpoint Path**: `/v1/dividends`
- **Data Selector**: `result.output.dividends`

### Stock Splits
- **Endpoint Path**: `/v1/stocksplits`
- **Data Selector**: `result.output.stocksplits`
- **Primary Key**: `ticker`, `date`

### Historical Candlestick
- **Endpoint Path**: `/v1/historicalcandlestick`
- **Data Selector**: `result.output.daily_stock_data`
- **Primary Key**: `ticker`, `date`

### Macro Calendar
- **Endpoint Path**: `/v1/macrocalendar`
- **Data Selector**: `result.output`

### Macro Indicator
- **Endpoint Path**: `/v1/macroindicator`
- **Data Selector**: `result.output`

### Commodities
- **Endpoint Path**: `/v1/commodities`
- **Data Selector**: `result.output`
- **Primary Key**: `commodity_name`, `datetime`

### Benchmark
- **Endpoint Path**: `/v1/benchmark`
- **Data Selector**: `result.output`
- **Primary Key**: `datetime`, `country`, `benchmark`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with various data processing systems. Each resource's schema includes fields such as `country`, `datetime`, `ticker`, and others, with appropriate data types and nullability settings.