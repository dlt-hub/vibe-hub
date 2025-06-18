# Overview

This document provides a guide for integrating with a third-party logistics API using a RESTful approach. The integration supports various resources such as users, orders, stores, carriers, products, webhooks, customers, shipments, warehouses, fulfillments, and marketplaces. The API requires authentication via API key and secret, and supports pagination and error handling mechanisms.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/v1/orders",
                "data_selector": "orders",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "pageSize",
                    "limit": 500
                }
            }
        },
        {
            "name": "stores",
            "endpoint": {
                "path": "/v1/stores",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "carriers",
            "endpoint": {
                "path": "/v1/carriers",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "products",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "pageSize",
                    "limit": 500
                }
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/v1/webhooks",
                "data_selector": "webhooks",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "customers",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "pageSize",
                    "limit": 500
                }
            }
        },
        {
            "name": "shipments",
            "endpoint": {
                "path": "/v1/shipments",
                "data_selector": "shipments",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "pageSize",
                    "limit": 500
                }
            }
        },
        {
            "name": "warehouses",
            "endpoint": {
                "path": "/v1/warehouses",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "fulfillments",
            "endpoint": {
                "path": "/v1/fulfillments",
                "data_selector": "fulfillments",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "pageSize",
                    "limit": 500
                }
            }
        },
        {
            "name": "marketplaces",
            "endpoint": {
                "path": "/v1/marketplaces",
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

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: API Key
  - `password`: API Secret

## Resources

### Users
- **Endpoint Path**: `/v1/users`
- **Pagination**: Single page
- **Data Selector**: `data`

### Orders
- **Endpoint Path**: `/v1/orders`
- **Pagination**: Page number
- **Data Selector**: `orders`
- **Pagination Parameters**: `page`, `pageSize`

### Stores
- **Endpoint Path**: `/v1/stores`
- **Pagination**: Single page
- **Data Selector**: `data`

### Carriers
- **Endpoint Path**: `/v1/carriers`
- **Pagination**: Single page
- **Data Selector**: `data`

### Products
- **Endpoint Path**: `/v1/products`
- **Pagination**: Page number
- **Data Selector**: `products`
- **Pagination Parameters**: `page`, `pageSize`

### Webhooks
- **Endpoint Path**: `/v1/webhooks`
- **Pagination**: Single page
- **Data Selector**: `webhooks`

### Customers
- **Endpoint Path**: `/v1/customers`
- **Pagination**: Page number
- **Data Selector**: `customers`
- **Pagination Parameters**: `page`, `pageSize`

### Shipments
- **Endpoint Path**: `/v1/shipments`
- **Pagination**: Page number
- **Data Selector**: `shipments`
- **Pagination Parameters**: `page`, `pageSize`

### Warehouses
- **Endpoint Path**: `/v1/warehouses`
- **Pagination**: Single page
- **Data Selector**: `data`

### Fulfillments
- **Endpoint Path**: `/v1/fulfillments`
- **Pagination**: Page number
- **Data Selector**: `fulfillments`
- **Pagination Parameters**: `page`, `pageSize`

### Marketplaces
- **Endpoint Path**: `/v1/marketplaces`
- **Pagination**: Single page
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2
- **Rate Limiting**: 
  - Header: `X-Rate-Limit-Reset`

## Schema

- **Users**: Fields include `userId`, `name`, `userName`
- **Orders**: Fields include `orderId`, `modifyDate`, `items`
- **Stores**: Fields include `storeId`, `storeName`, `createDate`
- **Carriers**: Fields include `shippingProviderId`, `name`, `code`
- **Products**: Fields include `productId`, `name`, `price`
- **Webhooks**: Fields include `WebHookID`, `Name`, `Url`
- **Customers**: Fields include `customerId`, `name`, `email`
- **Shipments**: Fields include `shipmentId`, `createDate`, `shipTo`
- **Warehouses**: Fields include `warehouseId`, `warehouseName`, `createDate`
- **Fulfillments**: Fields include `fulfillmentId`, `createDate`, `shipTo`
- **Marketplaces**: Fields include `name`, `marketplaceId`, `canRefresh`

This configuration provides a comprehensive setup for accessing and managing data from a third-party logistics API, ensuring efficient data retrieval and error handling.