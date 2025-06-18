# Overview

This document provides a configuration guide for integrating with a third-party marketing automation platform's REST API. The API supports data retrieval for various resources such as contacts, campaigns, carts, orders, and products. The integration is designed to be vendor-neutral and focuses on extracting data using a RESTful approach.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3/",
        "auth": {
            "type": "api_key",
            "name": "api_key",
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
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "response['paging']['next'] == None"
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/campaigns",
                "data_selector": "campaign",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "response['paging']['next'] == None"
                }
            }
        },
        {
            "name": "carts",
            "endpoint": {
                "path": "/carts",
                "data_selector": "carts",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "response['paging']['next'] == None"
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/orders",
                "data_selector": "orders",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "response['paging']['next'] == None"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/products",
                "data_selector": "products",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "response['paging']['next'] == None"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is injected into the request header.

## Resources

### Contacts
- **Endpoint Path**: `/contacts`
- **Data Selector**: `contacts`
- **Primary Key**: `contactID`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.

### Campaigns
- **Endpoint Path**: `/campaigns`
- **Data Selector**: `campaign`
- **Primary Key**: `campaignID`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.

### Carts
- **Endpoint Path**: `/carts`
- **Data Selector**: `carts`
- **Primary Key**: `cartID`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.

### Orders
- **Endpoint Path**: `/orders`
- **Data Selector**: `orders`
- **Primary Key**: `orderID`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.

### Products
- **Endpoint Path**: `/products`
- **Data Selector**: `products`
- **Primary Key**: `productID`
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3 retries with an exponential backoff factor of 2.
- **Fallback Behavior**: Stop condition for pagination is when the `next` cursor is `None`.

## Schema

The schema for each resource is defined with neutral field names and types. For example, the `contacts` resource includes fields like `contactID`, `email`, `firstName`, and `lastName`, with appropriate data types and nullability. Each resource's schema is designed to be flexible and accommodate additional properties as needed.