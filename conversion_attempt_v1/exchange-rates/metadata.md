# Overview

This document provides a configuration example for integrating with a third-party exchange rates API using dltHub's REST API source. The integration supports fetching exchange rate data through a RESTful API, allowing for seamless data synchronization.

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
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "exchange_rates",
            "endpoint": {
                "path": "/v1/exchange-rates",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Resource Name**: exchange_rates
- **Endpoint Path**: `/v1/exchange-rates`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `rate`, `currency`, and `timestamp`.
- **Field Types**: All fields are treated as strings or numbers, depending on the data type returned by the API.
- **Nullability**: Fields are nullable unless specified otherwise by the API documentation.

This configuration provides a clean and vendor-neutral setup for integrating with a third-party exchange rates API using dltHub's REST API source. It abstracts away any proprietary or branded content, ensuring a generic and reusable integration pattern.