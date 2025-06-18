# Overview

This document provides a vendor-neutral guide for integrating with a third-party financial API using dltHub's REST API source format. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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
            "name": "financial_transactions",
            "endpoint": {
                "path": "/v1/transactions",
                "data_selector": "transactions",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

## Resources

- **Resource Name**: `financial_transactions`
- **Endpoint Path**: `/v1/transactions`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `transactions` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `transaction_id`, `amount`, `currency`, and `date`.
- **Types**: Fields are typed according to their data nature, e.g., `transaction_id` as a string, `amount` as a decimal, `currency` as a string, and `date` as a date.
- **Nullability**: Fields are marked as nullable or non-nullable based on the API's data model.

This guide ensures a clean and efficient integration with the third-party financial API, leveraging dltHub's capabilities for REST API data extraction.