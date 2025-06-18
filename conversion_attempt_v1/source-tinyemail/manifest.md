# Overview

This document provides a configuration guide for integrating with a third-party email marketing API using dltHub's REST API source format. The integration allows for data extraction from various resources such as campaigns, contacts, sender details, and contact members. The API supports pagination and requires API key-based authentication.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "api_key",
            "name": "X-API-KEY",
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
            "name": "campaigns",
            "endpoint": {
                "path": "/campaign",
                "data_selector": "campaigns.content",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 20,
                    "start_from_page": 0
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "contacts"
            }
        },
        {
            "name": "sender_details",
            "endpoint": {
                "path": "/sender-details",
                "data_selector": "senderDetailses"
            }
        },
        {
            "name": "contact_members",
            "endpoint": {
                "path": "/contacts/{id}/members",
                "data_selector": "members.content",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 100,
                    "start_from_page": 1
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Configuration**: The API key should be stored securely and accessed via `dlt.secrets["api_key"]`. It is injected into the request header with the name `X-API-KEY`.

## Resources

### Campaigns
- **Endpoint Path**: `/campaign`
- **Data Selector**: `campaigns.content`
- **Pagination**: Page number-based, with parameters `page` and `size`. Starts from page 0 with a limit of 20 items per page.

### Contacts
- **Endpoint Path**: `/contacts`
- **Data Selector**: `contacts`

### Sender Details
- **Endpoint Path**: `/sender-details`
- **Data Selector**: `senderDetailses`

### Contact Members
- **Endpoint Path**: `/contacts/{id}/members`
- **Data Selector**: `members.content`
- **Pagination**: Page number-based, with parameters `page` and `size`. Starts from page 1 with a limit of 100 items per page.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a maximum of 3 retries and a backoff factor of 2.

## Schema

### Campaigns
- **Fields**: Includes fields such as `bounced`, `clicked`, `delivered`, `id`, `open`, `status`, etc.
- **Primary Key**: `id`

### Contacts
- **Fields**: Includes fields such as `id`, `name`, `numberOfMembers`.
- **Primary Key**: `id`

### Sender Details
- **Fields**: Includes fields such as `address`, `city`, `country`, `email`, `id`, `name`.
- **Primary Key**: `id`

### Contact Members
- **Fields**: Includes fields such as `address1`, `city`, `email`, `firstName`, `lastName`.
- **Primary Key**: Not specified

This configuration provides a comprehensive setup for extracting data from the specified resources using a REST API source in dltHub, ensuring a clean and vendor-neutral integration.