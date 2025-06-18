# Overview

This document provides a configuration example for integrating with a third-party financial data API. The API offers access to various financial market data, including stocks, forex, ETFs, indices, and cryptocurrencies. The integration supports multiple resources such as stocks, forex pairs, cryptocurrencies, and more, each with specific endpoints and configurations.

# Configuration Example

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
            "name": "stocks",
            "endpoint": {
                "path": "/v1/stocks",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "forex_pairs",
            "endpoint": {
                "path": "/v1/forex_pairs",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "crypto_currencies",
            "endpoint": {
                "path": "/v1/cryptocurrencies",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "funds",
            "endpoint": {
                "path": "/v1/funds",
                "data_selector": "result.list",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "bonds",
            "endpoint": {
                "path": "/v1/bonds",
                "data_selector": "result.list",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "etfs",
            "endpoint": {
                "path": "/v1/etfs",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "commodities",
            "endpoint": {
                "path": "/v1/commodities",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "exchanges",
            "endpoint": {
                "path": "/v1/exchanges",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "cryptocurrency_exchanges",
            "endpoint": {
                "path": "/v1/cryptocurrency_exchanges",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "market_state",
            "endpoint": {
                "path": "/v1/market_state",
                "data_selector": [],
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "countries",
            "endpoint": {
                "path": "/v1/countries",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "technical_indicators",
            "endpoint": {
                "path": "/v1/technical_indicators",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "time_series",
            "endpoint": {
                "path": "/v1/time_series",
                "data_selector": "values",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [404],
                    "max_retries": 0,
                    "backoff_factor": 0
                }
            }
        },
        {
            "name": "exchange_rate",
            "endpoint": {
                "path": "/v1/exchange_rate",
                "data_selector": [],
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "quote",
            "endpoint": {
                "path": "/v1/quote",
                "data_selector": [],
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "price",
            "endpoint": {
                "path": "/v1/price",
                "data_selector": [],
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "eod_price",
            "endpoint": {
                "path": "/v1/eod",
                "data_selector": [],
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "mutual_funds",
            "endpoint": {
                "path": "/v1/mutual_funds/list",
                "data_selector": "result.list",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the query parameters of the request.

# Resources

Each resource corresponds to a specific endpoint and data type. Below are the details for each resource:

- **Resource Name**: Abstracted from the original name.
- **Endpoint Path**: Generalized to `/v1/resource_name`.
- **HTTP Method**: GET (default for all resources).
- **Data Selector**: Specifies the path to extract data from the API response.
- **Pagination Strategy**: All resources use a `single_page` strategy, indicating no pagination.
- **Error Handling**: Configured to retry on status code 429 with a maximum of 3 retries and a backoff factor of 60 seconds.

# Error Handling

- **Retry/Backoff Logic**: Configured to handle rate limiting (HTTP 429) with a constant backoff strategy.
- **Known HTTP Response Codes**: 429 for rate limiting.
- **Fallback/Handling Behavior**: Retry up to 3 times with a 60-second backoff for rate-limited requests.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for most resources is `symbol`, except for resources like `exchanges` and `market_state`, which use `code`. The schema includes fields such as `type`, `country`, `currency`, `exchange`, `name`, and `symbol`, with types specified as `string` or `null`.