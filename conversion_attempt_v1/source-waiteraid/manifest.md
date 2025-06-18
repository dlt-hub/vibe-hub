# Overview

This document provides a configuration example for integrating with a third-party booking API using the dltHub REST API source format. The integration allows for the retrieval of booking data through a POST request to a specified endpoint. The configuration includes authentication, resource setup, and schema definition.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "auth_hash",
            "api_key": dlt.secrets["auth_hash"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "booking",
            "endpoint": {
                "path": "/v1/resource",
                "http_method": "POST",
                "params": {
                    "date": dlt.config["start_date"],
                    "restid": dlt.config["restid"]
                },
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
- **Secrets**: 
  - `auth_hash`: The API key used for authentication, stored securely in dlt secrets.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `booking`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: `POST`
- **Pagination Strategy**: `single_page` (no pagination)
- **Data Selector**: The data is extracted from the root of the response.

## Error Handling

- **Retry Logic**: Not specified in the YAML, but can be configured with retry strategies such as exponential backoff.
- **HTTP Response Codes**: Handle common HTTP errors like 429 (Too Many Requests) and 500-series server errors with retries.

## Schema

The schema defines the structure of the booking data, with fields and their types. All fields are nullable, and the schema includes nested objects for complex data types.

- **Fields**:
  - `all_seated`: Nullable number
  - `amount`: Nullable number
  - `arrived`: Nullable number
  - `booking_date`: Nullable number
  - `children_amount`: Nullable number
  - `comment`: Nullable string
  - `confirmed`: Nullable number
  - `date`: Nullable string
  - `end`: Nullable number
  - `guest`: Object with properties such as `address`, `city`, `email`, etc.
  - `guest_left`: Nullable number
  - `has_message`: Nullable number
  - `id`: Nullable number
  - `internet_booking`: Nullable number
  - `internet_booking_confirmed`: Nullable number
  - `langid`: Nullable number
  - `length`: Nullable number
  - `meal`: Nullable string
  - `meal_abbr`: Nullable string
  - `paid`: Nullable number
  - `payActivated`: Nullable boolean
  - `payCharged`: Nullable boolean
  - `payClosed`: Nullable boolean
  - `payStarted`: Nullable boolean
  - `placed`: Nullable number
  - `placed_manually`: Nullable number
  - `products`: Nullable number
  - `start`: Nullable number
  - `status`: Nullable string
  - `table_ids`: Nullable number
  - `tables`: Nullable number
  - `time`: Nullable string
  - `waitinbar`: Nullable number
  - `waitinlist`: Nullable number

- **Additional Properties**: The schema allows for additional properties not explicitly defined.