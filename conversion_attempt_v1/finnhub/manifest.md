# Overview

This document provides a configuration guide for integrating with a financial data platform's REST API. The API offers real-time data on stock markets, forex, and cryptocurrencies, along with fundamental data, economic indicators, and alternative data for global markets. This integration allows developers and financial institutions to incorporate market intelligence into applications, develop trading algorithms, and monitor investment performance. The API supports a variety of financial metrics, including earnings reports, company profiles, and news.

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
            "name": "market_news",
            "endpoint": {
                "path": "/news",
                "params": {
                    "category": "{{config['market_news_category']}}"
                },
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_symbols",
            "endpoint": {
                "path": "/stock/symbol",
                "params": {
                    "exchange": "{{config['exchange']}}"
                },
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "basic_financial_report",
            "endpoint": {
                "path": "/stock/financials-reported",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "company_profile",
            "endpoint": {
                "path": "/stock/profile2",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "sec_filings",
            "endpoint": {
                "path": "/stock/filings",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "insider_transactions",
            "endpoint": {
                "path": "/stock/insider-transactions",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "insider_sentiment",
            "endpoint": {
                "path": "/stock/insider-sentiment",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "company_news",
            "endpoint": {
                "path": "/company-news",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_recommendations",
            "endpoint": {
                "path": "/stock/recommendation",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "earnings_surprises",
            "endpoint": {
                "path": "/stock/earnings",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_quote",
            "endpoint": {
                "path": "/quote",
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
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`.

## Resources

### Market News
- **Endpoint Path**: `/news`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Stock Symbols
- **Endpoint Path**: `/stock/symbol`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Basic Financial Report
- **Endpoint Path**: `/stock/financials-reported`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Company Profile
- **Endpoint Path**: `/stock/profile2`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### SEC Filings
- **Endpoint Path**: `/stock/filings`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Insider Transactions
- **Endpoint Path**: `/stock/insider-transactions`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Insider Sentiment
- **Endpoint Path**: `/stock/insider-sentiment`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Company News
- **Endpoint Path**: `/company-news`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Stock Recommendations
- **Endpoint Path**: `/stock/recommendation`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Earnings Surprises
- **Endpoint Path**: `/stock/earnings`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Stock Quote
- **Endpoint Path**: `/quote`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.

## Schema

- **Fields**: Use neutral field names such as `analytics_id` instead of specific identifiers.
- **Types and Nullability**: Ensure fields are consistent with the provided schema and support null values where applicable.