# Overview

This document provides a configuration guide for integrating with a third-party marketing platform's REST API. The integration allows for the extraction of records from various resources such as communities, contacts, tags, contact fields, and course resources. The API supports cursor-based pagination and requires API key authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "X-API-Key",
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
            "name": "communities",
            "endpoint": {
                "path": "/community/communities",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "response.hasMore is false"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "response.hasMore is false"
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/tags",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "response.hasMore is false"
                }
            }
        },
        {
            "name": "contact_fields",
            "endpoint": {
                "path": "/contact_fields",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "response.hasMore is false"
                }
            }
        },
        {
            "name": "course_resources",
            "endpoint": {
                "path": "/school/courses",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "response.hasMore is false"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: `X-API-Key`
- **Location**: Header
- **Secret Key**: `api_key`

# Resources

## Communities
- **Endpoint Path**: `/community/communities`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `startingAfter` parameter
- **Data Selector**: `items`

## Contacts
- **Endpoint Path**: `/contacts`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `startingAfter` parameter
- **Data Selector**: `items`

## Tags
- **Endpoint Path**: `/tags`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `startingAfter` parameter
- **Data Selector**: `items`

## Contact Fields
- **Endpoint Path**: `/contact_fields`
- **Pagination**: Cursor-based with `startingAfter` parameter
- **Data Selector**: `items`

## Course Resources
- **Endpoint Path**: `/school/courses`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `startingAfter` parameter
- **Data Selector**: `items`

# Error Handling

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

## Communities
- **Fields**: `domainName`, `id`, `name`, `path`
- **Required**: `id`

## Contacts
- **Fields**: `bounced`, `email`, `fields`, `id`, `locale`, `needsConfirmation`, `registeredAt`, `tags`, `unsubscribed`
- **Required**: `id`

## Tags
- **Fields**: `createdAt`, `id`, `name`
- **Required**: `id`

## Contact Fields
- **Fields**: `fieldName`, `slug`

## Course Resources
- **Fields**: `active`, `domainName`, `id`, `locale`, `modules`, `name`, `path`
- **Required**: `id`