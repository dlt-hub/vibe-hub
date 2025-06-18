# Overview

This document provides a configuration example for integrating with a third-party currency exchange rates API using the dltHub REST API source. The integration allows for the retrieval of historical exchange rate data based on a specified start date. The API supports incremental synchronization using a timestamp-based cursor.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "app_id",
            "api_key": dlt.secrets["app_id"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "timestamp",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "currency_exchange_rates",
            "endpoint": {
                "path": "/v1/historical/{date}.json",
                "params": {
                    "base": dlt.config["base"]
                },
                "data_selector": "rates",
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
- **Secrets**: 
  - `api_key`: The API key is stored securely in dlt's secrets management system under the key `app_id`.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `currency_exchange_rates`
- **Endpoint Path**: `/v1/historical/{date}.json`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: The data is extracted from the `rates` field in the response.

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP error codes such as 429 (Too Many Requests) and 500 (Internal Server Error) with retries.
- **Backoff Strategy**: Use an exponential backoff strategy for retries.

## Schema

The schema defines the structure of the data retrieved from the API. It includes fields for various currency exchange rates, each represented as a number. The schema also includes a `timestamp` field indicating when the exchange rates were last updated.

- **Fields**:
  - `base`: The base currency against which exchange rates are provided.
  - `disclaimer`: Information about the usage rights or restrictions related to the provided data.
  - `license`: The type of license under which the exchange rate data is made available.
  - `rates`: Contains exchange rates data for various currencies.
  - `timestamp`: The UNIX timestamp indicating when the exchange rates were last updated.

- **Currency Fields**: Each currency field (e.g., `USD`, `EUR`) is a number representing the exchange rate against the base currency.