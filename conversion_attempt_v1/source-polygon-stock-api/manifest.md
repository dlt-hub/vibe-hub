# Overview

This document provides a configuration guide for integrating with a third-party stock data API using the dltHub REST API source format. The integration allows for the retrieval of stock market data, including prices, volumes, and other relevant metrics over specified time periods.

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
        "primary_key": "timestamp",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "stock_data",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "results",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Configuration**: The API key is injected into the request header. The key should be stored securely using `dlt.secrets`.

## Resources

- **Resource Name**: stock_data
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: Single page (no pagination)
- **Data Selector**: `results`

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors, such as network issues or server unavailability.
- **HTTP Response Codes**: Handle common HTTP status codes like 429 (Too Many Requests) and 500 (Internal Server Error) with appropriate retry mechanisms.

## Schema

The schema defines the structure of the data retrieved from the API. The fields are generalized to ensure vendor neutrality:

- **closing_price** (`c`): Closing price of the stock at a specific time (nullable, number)
- **highest_price** (`h`): Highest price the stock reached during a given period (nullable, number)
- **lowest_price** (`l`): Lowest price the stock dropped to within a certain timeframe (nullable, number)
- **stock_name** (`n`): Name of the stock or company (nullable, number)
- **opening_price** (`o`): Opening price of the stock at a particular time (nullable, number)
- **timestamp** (`t`): Timestamp of the stock data (nullable, integer)
- **volume** (`v`): Volume of stocks traded at a specific time (nullable, number)
- **volume_weighted_average_price** (`vw`): Volume-weighted average price of the stock (nullable, number)
- **transaction_time** (`otc`): Time when the stock transaction occurred (nullable, boolean)

All fields are optional and can be null, reflecting the variability in data availability.

This configuration provides a clean, vendor-neutral setup for accessing stock data through a REST API, ensuring compatibility with dltHub's data integration framework.