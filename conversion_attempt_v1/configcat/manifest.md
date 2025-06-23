# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources such as organizations, organization members, products, tags, and environments. The API uses basic authentication and supports partitioned data retrieval.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
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
            "name": "organizations",
            "endpoint": {
                "path": "/organizations",
                "data_selector": "data"
            }
        },
        {
            "name": "organization_members",
            "endpoint": {
                "path": "/organizations/{organizationId}/members",
                "data_selector": "data"
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/products",
                "data_selector": "data"
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/products/{productId}/tags",
                "data_selector": "data"
            }
        },
        {
            "name": "environments",
            "endpoint": {
                "path": "/products/{productId}/environments",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: The username for basic authentication.
  - `password`: The password for basic authentication.

# Resources

### Organizations
- **Endpoint Path**: `/organizations`
- **Primary Key**: `organizationId`
- **Data Selector**: `data`

### Organization Members
- **Endpoint Path**: `/organizations/{organizationId}/members`
- **Primary Key**: `userId`
- **Data Selector**: `data`
- **Partitioning**: Based on `organizationId` from the organizations resource.

### Products
- **Endpoint Path**: `/products`
- **Primary Key**: `productId`
- **Data Selector**: `data`

### Tags
- **Endpoint Path**: `/products/{productId}/tags`
- **Primary Key**: `tagId`
- **Data Selector**: `data`
- **Partitioning**: Based on `productId` from the products resource.

### Environments
- **Endpoint Path**: `/products/{productId}/environments`
- **Primary Key**: `environmentId`
- **Data Selector**: `data`
- **Partitioning**: Based on `productId` from the products resource.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

### Organizations
- **Fields**:
  - `name`: string
  - `organizationId`: string

### Organization Members
- **Fields**:
  - `email`: string
  - `fullName`: string
  - `userId`: string

### Products
- **Fields**:
  - `description`: string
  - `name`: string
  - `order`: integer
  - `organizationId`: object
  - `productId`: string
  - `reasonRequired`: boolean

### Tags
- **Fields**:
  - `color`: string
  - `name`: string
  - `product`: object
  - `tagId`: integer

### Environments
- **Fields**:
  - `description`: null or string
  - `color`: null or string
  - `environmentId`: null or string
  - `name`: null or string
  - `order`: null or integer
  - `product`: object
  - `reasonRequired`: null or boolean

This configuration provides a comprehensive setup for accessing and synchronizing data from the specified resources using dltHub's REST API source format.