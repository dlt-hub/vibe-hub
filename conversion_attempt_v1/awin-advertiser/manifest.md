# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization for campaign performance and transaction data, utilizing a bearer token for authentication and supporting incremental data sync based on date fields.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "campaign_performance",
            "endpoint": {
                "path": "/v1/advertisers/{advertiser_id}/reports/campaign",
                "params": {
                    "interval": "day"
                },
                "data_selector": "result",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "date",
                    "cursor_path": "date",
                    "step": "P1D"
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v1/advertisers/{advertiser_id}/transactions",
                "params": {
                    "showBasketProducts": "True"
                },
                "data_selector": "",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "transactionDate",
                    "cursor_path": "transactionDate",
                    "step": "P1D"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Campaign Performance

- **Resource Name**: campaign_performance
- **Endpoint Path**: `/v1/advertisers/{advertiser_id}/reports/campaign`
- **HTTP Method**: GET
- **Data Selector**: `result`
- **Primary Key**: `date`, `publisherId`, `campaign`
- **Pagination Strategy**: Cursor-based on `date` with a daily step increment

### Transactions

- **Resource Name**: transactions
- **Endpoint Path**: `/v1/advertisers/{advertiser_id}/transactions`
- **HTTP Method**: GET
- **Data Selector**: None
- **Primary Key**: `id`
- **Pagination Strategy**: Cursor-based on `transactionDate` with a daily step increment

## Error Handling

- **Retry Logic**: Implement retry on common HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

### Campaign Performance

- **Fields**:
  - `campaign`: string
  - `date`: string
  - `publisherId`: string
  - `publisherName`: string or null
  - `totals`: object or null
    - `commissionAmount`: object or null
      - `approved`: string or null
      - `bonus`: string or null
      - `declined`: string or null
      - `pending`: string or null
      - `total`: string or null
    - `quantity`: object or null
      - `approved`: number or null
      - `bonus`: number or null
      - `click`: number or null
      - `declined`: number or null
      - `pending`: number or null
      - `total`: number or null
    - `saleAmount`: object or null
      - `approved`: string or null
      - `bonus`: string or null
      - `declined`: string or null
      - `pending`: string or null
      - `total`: string or null

### Transactions

- **Fields**: Schema is flexible and allows additional properties.