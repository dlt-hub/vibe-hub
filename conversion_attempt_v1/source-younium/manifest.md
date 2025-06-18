# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, such as accounts, bookings, invoices, products, and subscriptions. The API uses a cursor-based pagination strategy and requires authentication via a custom authenticator.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"],
            "legal_entity": dlt.secrets["legal_entity"],
            "grant_type": "password",
            "client_id": "apiclient",
            "scope": "openid api profile"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "account",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "nextPage",
                    "stop_condition": "not response.get('nextPage', {})"
                }
            }
        },
        {
            "name": "booking",
            "endpoint": {
                "path": "/v1/bookings",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "nextPage",
                    "stop_condition": "not response.get('nextPage', {})"
                }
            }
        },
        {
            "name": "invoice",
            "endpoint": {
                "path": "/v1/invoices",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "nextPage",
                    "stop_condition": "not response.get('nextPage', {})"
                }
            }
        },
        {
            "name": "product",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "nextPage",
                    "stop_condition": "not response.get('nextPage', {})"
                }
            }
        },
        {
            "name": "subscription",
            "endpoint": {
                "path": "/v1/subscriptions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "nextPage",
                    "stop_condition": "not response.get('nextPage', {})"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `username`: Your API username
  - `password`: Your API password
  - `legal_entity`: The legal entity from which data should be pulled
- **Grant Type**: Password
- **Client ID**: apiclient
- **Scope**: openid api profile

## Resources

### Account
- **Endpoint Path**: `/v1/accounts`
- **Pagination**: Cursor-based
- **Data Selector**: `data`

### Booking
- **Endpoint Path**: `/v1/bookings`
- **Pagination**: Cursor-based
- **Data Selector**: `data`

### Invoice
- **Endpoint Path**: `/v1/invoices`
- **Pagination**: Cursor-based
- **Data Selector**: `data`

### Product
- **Endpoint Path**: `/v1/products`
- **Pagination**: Cursor-based
- **Data Selector**: `data`

### Subscription
- **Endpoint Path**: `/v1/subscriptions`
- **Pagination**: Cursor-based
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: [429, 500, 502, 503, 504]
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

Each resource has a schema that includes fields such as unique identifiers, descriptions, and various financial metrics. The schema is designed to be flexible, allowing for additional properties and custom fields. Field names are neutral and generalized to ensure compatibility with various data models.