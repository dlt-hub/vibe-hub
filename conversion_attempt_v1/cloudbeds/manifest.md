# Overview

This document provides a configuration example for integrating with a third-party hospitality platform API using dltHub's REST API source. The integration allows for data ingestion from various resources such as guests, hotels, rooms, reservations, transactions, and packages. The API supports pagination and requires authentication via an API key.

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
            "name": "guests",
            "endpoint": {
                "path": "/v1/resource/getGuestList",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
                }
            }
        },
        {
            "name": "hotels",
            "endpoint": {
                "path": "/v1/resource/getHotels",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
                }
            }
        },
        {
            "name": "rooms",
            "endpoint": {
                "path": "/v1/resource/getRoomBlocks",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
                }
            }
        },
        {
            "name": "reservations",
            "endpoint": {
                "path": "/v1/resource/getReservations",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v1/resource/getTransactions",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
                }
            }
        },
        {
            "name": "packages",
            "endpoint": {
                "path": "/v1/resource/getItems",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
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

### Guests
- **Endpoint Path**: `/v1/resource/getGuestList`
- **Primary Key**: `guestID`
- **Pagination**: Page number with `pageNumber` and `pageSize` parameters

### Hotels
- **Endpoint Path**: `/v1/resource/getHotels`
- **Pagination**: Page number with `pageNumber` and `pageSize` parameters

### Rooms
- **Endpoint Path**: `/v1/resource/getRoomBlocks`
- **Primary Key**: `propertyID`
- **Pagination**: Page number with `pageNumber` and `pageSize` parameters

### Reservations
- **Endpoint Path**: `/v1/resource/getReservations`
- **Primary Key**: `reservationID`
- **Pagination**: Page number with `pageNumber` and `pageSize` parameters

### Transactions
- **Endpoint Path**: `/v1/resource/getTransactions`
- **Primary Key**: `transactionID`
- **Pagination**: Page number with `pageNumber` and `pageSize` parameters

### Packages
- **Endpoint Path**: `/v1/resource/getItems`
- **Primary Key**: `itemID`
- **Pagination**: Page number with `pageNumber` and `pageSize` parameters

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

Each resource has a defined schema with fields, types, and nullability. The field names are generalized to maintain neutrality. For example, `guestID` is used as a primary key for the guests resource.