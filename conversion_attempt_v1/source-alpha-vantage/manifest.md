# Overview

This document provides a configuration guide for integrating with a third-party financial data API using the dltHub REST API source format. The integration allows for the retrieval of various time series data, including intraday, daily, weekly, and monthly data, as well as global quotes. The API supports different data intervals and can return both adjusted and unadjusted data.

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
        "primary_key": "timestamp",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "time_series_intraday",
            "endpoint": {
                "path": "/query",
                "params": {
                    "function": "TIME_SERIES_INTRADAY",
                    "symbol": dlt.secrets["symbol"],
                    "datatype": "json",
                    "outputsize": dlt.secrets["outputsize"],
                    "interval": dlt.secrets["interval"],
                    "adjusted": dlt.secrets["adjusted"]
                },
                "data_selector": "Time Series ({{ config['interval'] }})",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "time_series_daily",
            "endpoint": {
                "path": "/query",
                "params": {
                    "function": "TIME_SERIES_DAILY",
                    "symbol": dlt.secrets["symbol"],
                    "datatype": "json",
                    "outputsize": dlt.secrets["outputsize"],
                    "interval": dlt.secrets["interval"],
                    "adjusted": dlt.secrets["adjusted"]
                },
                "data_selector": "Time Series (Daily)",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "time_series_weekly",
            "endpoint": {
                "path": "/query",
                "params": {
                    "function": "TIME_SERIES_WEEKLY",
                    "symbol": dlt.secrets["symbol"],
                    "datatype": "json",
                    "outputsize": dlt.secrets["outputsize"],
                    "interval": dlt.secrets["interval"],
                    "adjusted": dlt.secrets["adjusted"]
                },
                "data_selector": "Weekly Time Series",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "quote",
            "endpoint": {
                "path": "/query",
                "params": {
                    "function": "GLOBAL_QUOTE",
                    "symbol": dlt.secrets["symbol"],
                    "datatype": "json",
                    "outputsize": dlt.secrets["outputsize"],
                    "interval": dlt.secrets["interval"],
                    "adjusted": dlt.secrets["adjusted"]
                },
                "data_selector": "Global Quote",
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
- **Secrets or Config Values**:
  - `api_key`: The API key for authentication.
  - `symbol`: The stock symbol to query.
  - `interval`: The time interval for intraday data.
  - `adjusted`: Boolean indicating if adjusted data is required.
  - `outputsize`: Specifies whether to return full or compact data.

## Resources

### Resource: time_series_intraday
- **Endpoint Path**: `/query`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `Time Series ({{ config['interval'] }})`

### Resource: time_series_daily
- **Endpoint Path**: `/query`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `Time Series (Daily)`

### Resource: time_series_weekly
- **Endpoint Path**: `/query`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `Weekly Time Series`

### Resource: quote
- **Endpoint Path**: `/query`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `Global Quote`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### time_series_intraday
- **Fields**:
  - `open`: string or null
  - `high`: string or null
  - `low`: string or null
  - `close`: string or null
  - `volume`: string or null

### time_series_daily
- **Fields**:
  - `open`: string or null
  - `high`: string or null
  - `low`: string or null
  - `close`: string or null
  - `volume`: string or null

### time_series_weekly
- **Fields**:
  - `open`: string or null
  - `high`: string or null
  - `low`: string or null
  - `close`: string or null
  - `volume`: string or null

### quote
- **Fields**:
  - `symbol`: string or null
  - `open`: string or null
  - `high`: string or null
  - `low`: string or null
  - `price`: string or null
  - `volume`: string or null
  - `latest trading day`: string or null
  - `previous close`: string or null
  - `change`: string or null
  - `change percent`: string or null

This configuration provides a comprehensive setup for accessing financial data through a REST API, ensuring a clean and vendor-neutral integration.