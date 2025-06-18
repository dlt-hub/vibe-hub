# Overview

This document provides a configuration guide for integrating with a third-party financial data API using dltHub's REST API source format. The API allows extraction of various financial data, including company information, financial statements, price data, and filings. The integration supports multiple resources, each with specific endpoints and data extraction configurations.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "company_info",
            "endpoint": {
                "path": "/v1/companies/general/verbose",
                "data_selector": "data"
            }
        },
        {
            "name": "financial_statements",
            "endpoint": {
                "path": "/v1/companies/statements/verbose",
                "data_selector": "statements",
                "params": {
                    "statements": "pl,bs,cf,derived"
                }
            }
        },
        {
            "name": "price_data",
            "endpoint": {
                "path": "/v1/companies/prices/verbose",
                "data_selector": "data"
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/v1/companies/list",
                "data_selector": "data"
            }
        },
        {
            "name": "common_shares_outstanding",
            "endpoint": {
                "path": "/v1/companies/common-shares-outstanding",
                "data_selector": "data"
            }
        },
        {
            "name": "weighted_shares_outstanding",
            "endpoint": {
                "path": "/v1/companies/weighted-shares-outstanding",
                "data_selector": "data"
            }
        },
        {
            "name": "filings_by_company",
            "endpoint": {
                "path": "/v1/filings/by-company",
                "data_selector": "data"
            }
        },
        {
            "name": "filings_list",
            "endpoint": {
                "path": "/v1/filings/list",
                "data_selector": "contents",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 1000
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header under the name `Authorization`.

## Resources

### Company Info
- **Endpoint Path**: `/v1/companies/general/verbose`
- **Data Selector**: `data`
- **Primary Key**: `id`

### Financial Statements
- **Endpoint Path**: `/v1/companies/statements/verbose`
- **Data Selector**: `statements`
- **Parameters**: `statements=pl,bs,cf,derived`

### Price Data
- **Endpoint Path**: `/v1/companies/prices/verbose`
- **Data Selector**: `data`

### Companies
- **Endpoint Path**: `/v1/companies/list`
- **Data Selector**: `data`

### Common Shares Outstanding
- **Endpoint Path**: `/v1/companies/common-shares-outstanding`
- **Data Selector**: `data`

### Weighted Shares Outstanding
- **Endpoint Path**: `/v1/companies/weighted-shares-outstanding`
- **Data Selector**: `data`

### Filings by Company
- **Endpoint Path**: `/v1/filings/by-company`
- **Data Selector**: `data`
- **Primary Key**: `filingIdentifier`

### Filings List
- **Endpoint Path**: `/v1/filings/list`
- **Data Selector**: `contents`
- **Pagination**: Page number-based with `page` and `per-page` parameters, starting from page 0 with a limit of 1000 items per page.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with various data models. Each resource's schema is consistent with the data provided by the API and includes fields such as `id`, `name`, `industryName`, and financial metrics like `Net Income`, `Revenue`, etc.