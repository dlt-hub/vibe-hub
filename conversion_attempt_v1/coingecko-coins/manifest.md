# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports fetching market data and historical data for various resources. The API allows for both incremental and full data synchronization.

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
            "name": "market_data",
            "endpoint": {
                "path": "/v1/market_chart",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "historical_data",
            "endpoint": {
                "path": "/v1/history",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            },
            "incremental_sync": {
                "type": "datetime_cursor",
                "start_datetime": dlt.config["start_date"],
                "end_datetime": dlt.config.get("end_date", dlt.utils.now_utc().strftime("%d-%m-%Y")),
                "cursor_field": "date",
                "step": "P1D"
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: 
  - `api_key`: The API key for accessing the service, stored securely in dltHub secrets.

## Resources

### Market Data

- **Resource Name**: `market_data`
- **Endpoint Path**: `/v1/market_chart`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

### Historical Data

- **Resource Name**: `historical_data`
- **Endpoint Path**: `/v1/history`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`
- **Incremental Sync**: 
  - **Type**: `datetime_cursor`
  - **Cursor Field**: `date`
  - **Start Date**: Configurable via `start_date`
  - **End Date**: Configurable via `end_date` or defaults to current date
  - **Step**: Daily (`P1D`)

## Error Handling

- **Retry Logic**: Not explicitly defined in the YAML, but can be configured in dltHub with retry strategies for common HTTP status codes like 429, 500, etc.
- **Timeouts and Limits**: Not specified, but can be configured as needed.

## Schema

### Market Data Schema

- **Prices**: Array of arrays containing numbers
- **Market Caps**: Array of arrays containing numbers
- **Total Volumes**: Array of arrays containing numbers

### Historical Data Schema

- **ID**: String or null
- **Symbol**: String or null
- **Name**: String or null
- **Localization**: Object with language keys and string or null values
- **Image**: Object with `thumb` and `small` properties as string or null
- **Market Data**: Object with `current_price`, `market_cap`, and `total_volume` properties, each containing currency keys with number or null values
- **Community Data**: Object with various properties, some with number or null values
- **Developer Data**: Object with properties like `forks`, `stars`, etc., with number or null values
- **Public Interest Stats**: Object with properties like `alexa_rank` and `bing_matches`

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source.