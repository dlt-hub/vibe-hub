# Overview

This document provides a setup guide for integrating with a third-party email marketing and services platform via its REST API. The integration allows for the ingestion of various resources from the platform's API, enabling data synchronization and management.

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
            "name": "clients",
            "endpoint": {
                "path": "/v1/clients.json",
                "data_selector": "data"
            }
        },
        {
            "name": "admins",
            "endpoint": {
                "path": "/v1/admins.json",
                "data_selector": "data"
            }
        },
        {
            "name": "client_details",
            "endpoint": {
                "path": "/v1/clients/{client_id}.json",
                "data_selector": "data"
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/v1/clients/{client_id}/segments.json",
                "data_selector": "data"
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/v1/clients/{client_id}/templates.json",
                "data_selector": "data"
            }
        },
        {
            "name": "people",
            "endpoint": {
                "path": "/v1/clients/{client_id}/people.json",
                "data_selector": "data"
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/clients/{client_id}/tags.json",
                "data_selector": "data"
            }
        },
        {
            "name": "subscriber_lists",
            "endpoint": {
                "path": "/v1/clients/{client_id}/lists.json",
                "data_selector": "data"
            }
        },
        {
            "name": "suppression_lists",
            "endpoint": {
                "path": "/v1/clients/{client_id}/suppressionlist.json",
                "data_selector": "Results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "pagesize",
                    "limit": 1000
                }
            }
        },
        {
            "name": "sent_campaigns",
            "endpoint": {
                "path": "/v1/clients/{client_id}/campaigns.json",
                "data_selector": "Results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "pagesize",
                    "limit": 1000
                }
            }
        },
        {
            "name": "draft_campaigns",
            "endpoint": {
                "path": "/v1/clients/{client_id}/drafts.json",
                "data_selector": "data"
            }
        },
        {
            "name": "scheduled_campaigns",
            "endpoint": {
                "path": "/v1/clients/{client_id}/scheduled.json",
                "data_selector": "data"
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: Your API username
  - `password`: Your API password

## Resources

- **Resource Name**: clients
  - **Endpoint Path**: `/v1/clients.json`
  - **Primary Key**: `ClientID`

- **Resource Name**: admins
  - **Endpoint Path**: `/v1/admins.json`
  - **Primary Key**: `EmailAddress`

- **Resource Name**: client_details
  - **Endpoint Path**: `/v1/clients/{client_id}.json`
  - **Primary Key**: `ClientID`

- **Resource Name**: segments
  - **Endpoint Path**: `/v1/clients/{client_id}/segments.json`
  - **Primary Key**: `SegmentID`

- **Resource Name**: templates
  - **Endpoint Path**: `/v1/clients/{client_id}/templates.json`
  - **Primary Key**: `TemplateID`

- **Resource Name**: people
  - **Endpoint Path**: `/v1/clients/{client_id}/people.json`
  - **Primary Key**: `EmailAddress`

- **Resource Name**: tags
  - **Endpoint Path**: `/v1/clients/{client_id}/tags.json`
  - **Primary Key**: `Name`

- **Resource Name**: subscriber_lists
  - **Endpoint Path**: `/v1/clients/{client_id}/lists.json`
  - **Primary Key**: `ListID`

- **Resource Name**: suppression_lists
  - **Endpoint Path**: `/v1/clients/{client_id}/suppressionlist.json`
  - **Primary Key**: `EmailAddress`
  - **Pagination**: Page number with `page` and `pagesize` parameters

- **Resource Name**: sent_campaigns
  - **Endpoint Path**: `/v1/clients/{client_id}/campaigns.json`
  - **Primary Key**: `CampaignID`
  - **Pagination**: Page number with `page` and `pagesize` parameters

- **Resource Name**: draft_campaigns
  - **Endpoint Path**: `/v1/clients/{client_id}/drafts.json`
  - **Primary Key**: `CampaignID`

- **Resource Name**: scheduled_campaigns
  - **Endpoint Path**: `/v1/clients/{client_id}/scheduled.json`
  - **Primary Key**: `CampaignID`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

- **clients**: 
  - `ClientID`: string
  - `Name`: string or null

- **admins**: 
  - `EmailAddress`: string
  - `Name`: string or null
  - `Status`: string or null

- **client_details**: 
  - `ClientID`: string
  - `ApiKey`: string or null
  - `BasicDetails`: object or null
  - `BillingDetails`: object or null

- **segments**: 
  - `SegmentID`: string
  - `ListID`: string or null
  - `Title`: string or null

- **templates**: 
  - `TemplateID`: string
  - `Name`: string or null
  - `PreviewURL`: string or null
  - `ScreenshotURL`: string or null

- **people**: 
  - `EmailAddress`: string
  - `Name`: string or null
  - `AccessLevel`: number or null
  - `Status`: string or null

- **tags**: 
  - `Name`: string
  - `NumberOfCampaigns`: number or null

- **subscriber_lists**: 
  - `ListID`: string
  - `Name`: string or null

- **suppression_lists**: 
  - `EmailAddress`: string
  - `Date`: string or null
  - `State`: string or null
  - `SuppressionReason`: string or null

- **sent_campaigns**: 
  - `CampaignID`: string
  - `SentDate`: string
  - `FromEmail`: string or null
  - `FromName`: string or null
  - `Name`: string or null
  - `ReplyTo`: string or null
  - `Subject`: string or null
  - `Tags`: array or null
  - `TotalRecipients`: number or null
  - `WebVersionTextURL`: string or null
  - `WebVersionURL`: string or null

- **draft_campaigns**: 
  - `CampaignID`: string
  - `DateCreated`: string or null
  - `FromEmail`: string or null
  - `FromName`: string or null
  - `Name`: string or null
  - `PreviewTextURL`: string or null
  - `PreviewURL`: string or null
  - `ReplyTo`: string or null
  - `Subject`: string or null
  - `Tags`: array or null

- **scheduled_campaigns**: 
  - `CampaignID`: string
  - `DateCreated`: string or null
  - `DateScheduled`: string or null
  - `FromEmail`: string or null
  - `FromName`: string or null
  - `Name`: string or null
  - `PreviewTextURL`: string or null
  - `PreviewURL`: string or null
  - `ReplyTo`: string or null
  - `ScheduledTimeZone`: string or null
  - `Subject`: string or null
  - `Tags`: array or null