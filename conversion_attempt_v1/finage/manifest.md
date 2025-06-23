# Overview

This document provides a configuration guide for integrating with a third-party market data API. The API offers real-time data solutions for various financial instruments, including stocks, forex, and cryptocurrencies. The integration supports multiple data resources such as market news, technical indicators, economic calendars, and more.

# Configuration Example

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
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "market_news",
            "endpoint": {
                "path": "/news/market/{symbol}",
                "data_selector": "news",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "most_active_us_stocks",
            "endpoint": {
                "path": "/fnd/market-information/us/most-actives",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "technical_indicators",
            "endpoint": {
                "path": "/fnd/technical-indicator/{tech_indicator_type}/{time}/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "economic_calendar",
            "endpoint": {
                "path": "/fnd/economic-calendar",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "earning_calendar",
            "endpoint": {
                "path": "/fnd/earning-calendar",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "delisted_companies",
            "endpoint": {
                "path": "/fnd/delisted-companies/",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "ipo_calendar",
            "endpoint": {
                "path": "/fnd/ipo-calendar",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "historical_stock_split",
            "endpoint": {
                "path": "/fnd/historical-stock-splits/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "historical_dividends_calendar",
            "endpoint": {
                "path": "/fnd/historical-dividends/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "cash_flow_statements",
            "endpoint": {
                "path": "/fnd/cash-flow-statement/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "balance_sheet_statements",
            "endpoint": {
                "path": "/fnd/balance-sheet-statements/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "income_statement",
            "endpoint": {
                "path": "/fnd/income-statement/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "institutional_holders",
            "endpoint": {
                "path": "/fnd/funds/institutional-holder/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "mutual_fund_holder",
            "endpoint": {
                "path": "/fnd/funds/mutual-fund-holder/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "most_gainers",
            "endpoint": {
                "path": "/fnd/market-information/us/most-gainers",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "most_losers",
            "endpoint": {
                "path": "/fnd/market-information/us/most-losers",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "sector_performance",
            "endpoint": {
                "path": "/fnd/market-information/us/sector-performance",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "shares_float",
            "endpoint": {
                "path": "/fnd/shares-float/{symbol}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of each request.

# Resources

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized paths for each resource.
- **HTTP Method**: All requests use the GET method.
- **Pagination Strategy**: All resources use a `single_page` strategy, indicating no pagination is required.
- **Data Selector**: The field path for extracting data from the API response.

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP errors such as 429 (Too Many Requests) and 500 (Internal Server Error).
- **Backoff Strategy**: Use exponential backoff for retries to avoid overwhelming the API.

# Schema

- **Fields**: Each resource has a defined schema with fields, types, and nullability.
- **Neutral Field Names**: Use generic field names such as `symbol`, `date`, and `value` to maintain vendor neutrality.