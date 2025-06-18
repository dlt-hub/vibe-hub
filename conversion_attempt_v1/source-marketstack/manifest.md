# Overview

This document provides a configuration guide for integrating with a third-party financial data API. The API offers access to data from various global stock exchanges, including historical data, splits, and dividends. This guide outlines how to configure the API integration using dltHub's REST API source format.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "api_key",
            "name": "access_key",
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
            "name": "exchanges",
            "endpoint": {
                "path": "/exchanges",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "tickers",
            "endpoint": {
                "path": "/exchanges/{mic}/tickers",
                "data_selector": "data.tickers",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "historical_data",
            "endpoint": {
                "path": "/eod",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "splits",
            "endpoint": {
                "path": "/splits",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "dividends",
            "endpoint": {
                "path": "/dividends",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "offset"
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

### Exchanges
- **Endpoint Path**: `/exchanges`
- **Primary Key**: `mic`
- **Pagination Strategy**: Offset with `limit` and `offset` parameters
- **Data Selector**: `data`

### Tickers
- **Endpoint Path**: `/exchanges/{mic}/tickers`
- **Primary Key**: `symbol`
- **Pagination Strategy**: Offset with `limit` and `offset` parameters
- **Data Selector**: `data.tickers`

### Historical Data
- **Endpoint Path**: `/eod`
- **Primary Key**: `date`, `symbol`
- **Pagination Strategy**: Offset with `limit` and `offset` parameters
- **Data Selector**: `data`

### Splits
- **Endpoint Path**: `/splits`
- **Primary Key**: `date`, `symbol`
- **Pagination Strategy**: Offset with `limit` and `offset` parameters
- **Data Selector**: `data`

### Dividends
- **Endpoint Path**: `/dividends`
- **Primary Key**: `date`, `symbol`
- **Pagination Strategy**: Offset with `limit` and `offset` parameters
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry on status codes such as 429, 500, 502, 503, 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2.
- **Max Retries**: 3 attempts before failing.

## Schema

### Exchanges
- **Fields**: `acronym`, `city`, `country`, `country_code`, `currency`, `mic`, `name`, `timezone`, `website`
- **Primary Key**: `mic`

### Tickers
- **Fields**: `has_eod`, `has_intraday`, `name`, `symbol`
- **Primary Key**: `symbol`

### Historical Data
- **Fields**: `adj_close`, `adj_high`, `adj_low`, `adj_open`, `adj_volume`, `close`, `date`, `dividend`, `exchange`, `high`, `low`, `open`, `split_factor`, `symbol`, `volume`
- **Primary Key**: `date`, `symbol`

### Splits
- **Fields**: `date`, `split_factor`, `symbol`
- **Primary Key**: `date`, `symbol`

### Dividends
- **Fields**: `date`, `dividend`, `symbol`
- **Primary Key**: `date`, `symbol`

This configuration guide provides a comprehensive setup for accessing and synchronizing data from a third-party financial data API using dltHub's REST API source format.