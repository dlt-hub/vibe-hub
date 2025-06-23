# Overview

This document provides a configuration example for integrating with a third-party financial data API using dltHub's REST API source format. The integration allows for data extraction from various endpoints, such as stock lists, exchange symbols, and historical market capitalization.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "apikey",
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
            "name": "stocks",
            "endpoint": {
                "path": "/v1/stock/list",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "etfs",
            "endpoint": {
                "path": "/v1/etf/list",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_available_traded",
            "endpoint": {
                "path": "/v1/available-traded/list",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_cik_list",
            "endpoint": {
                "path": "/v1/cik_list",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_euronext_symbols",
            "endpoint": {
                "path": "/v1/symbol/available-euronext",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_exchange_symbols",
            "endpoint": {
                "path": "/v1/symbol/{exchange}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_available_indexes",
            "endpoint": {
                "path": "/v1/symbol/available-indexes",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "company_profile",
            "endpoint": {
                "path": "/v1/profile/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_screener",
            "endpoint": {
                "path": "/v1/stock-screener",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "params": {
                    "marketCapMoreThan": "{marketcapmorethan}",
                    "marketCapLowerThan": "{marketcaplowerthan}"
                }
            }
        },
        {
            "name": "historical_market_cap",
            "endpoint": {
                "path": "/v1/historical-market-capitalization/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "params": {
                    "limit": "500"
                }
            }
        },
        {
            "name": "delisted_companies",
            "endpoint": {
                "path": "/v1/delisted-companies",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "exchange_prices",
            "endpoint": {
                "path": "/v1/stock-price-change/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "all_realtime_stock_prices",
            "endpoint": {
                "path": "/v1/stock/full/real-time-price",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "all_fx_prices",
            "endpoint": {
                "path": "/v1/fx",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_historical_price",
            "endpoint": {
                "path": "/v1/historical-chart/{time_frame}/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "forex_list",
            "endpoint": {
                "path": "/v1/symbol/available-forex-currency-pairs",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "cryptocurrencies_list",
            "endpoint": {
                "path": "/v1/symbol/available-cryptocurrencies",
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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the query parameters.

## Resources

- **Resource Names**: Abstracted to generic names such as `stocks`, `etfs`, `stock_available_traded`, etc.
- **Endpoint Paths**: Generalized paths like `/v1/stock/list`, `/v1/etf/list`, etc.
- **Pagination Strategy**: All resources use a `single_page` pagination strategy.
- **Data Selector**: The data is extracted using the `data` field.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Timeouts**: Handle timeouts and data-limit messages gracefully.

## Schema

- **Fields**: Use neutral field names such as `symbol`, `price`, `exchange`, etc.
- **Types**: Ensure consistency with the YAML schema definitions, using types like `string`, `number`, and `boolean`.
- **Nullability**: Fields can be nullable, as indicated in the schema.