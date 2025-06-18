# Overview

This document provides a configuration example for integrating with a third-party travel data API using dltHub's REST API source format. The integration allows for the retrieval of hotel and flight booking data, supporting incremental synchronization based on modification timestamps.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/oauth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "uuid",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "bookings",
            "endpoint": {
                "path": "/v1/bookings",
                "params": {
                    "bookingType": "FLIGHT"
                },
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 50
                }
            },
            "incremental_sync": {
                "cursor_field": "lastModified",
                "start_datetime": dlt.config["start_date"],
                "end_datetime": "now"
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Client ID**: `client_id` (stored in dlt secrets)
- **Client Secret**: `client_secret` (stored in dlt secrets)
- **Token URL**: `https://api.example.com/oauth/token`

## Resources

- **Resource Name**: bookings
- **Endpoint Path**: `/v1/bookings`
- **HTTP Method**: GET
- **Pagination Strategy**: Page number
  - **Page Parameter**: `page`
  - **Limit Parameter**: `size`
  - **Limit**: 50
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504) with a maximum of 3 retries and a backoff factor of 2.

## Schema

The schema for the bookings resource includes fields such as:

- **uuid**: Unique identifier for the booking (string, required)
- **lastModified**: Timestamp of the last modification (string, required)
- **bookingType**: Type of booking (e.g., FLIGHT) (string)
- **basePrice**: Base price of the booking (number)
- **currency**: Currency of the transaction (string)

The schema supports additional properties and includes various fields related to booking details, such as approval status, booker information, and payment details. All fields are generalized and vendor-neutral.