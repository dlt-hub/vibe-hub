# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various data resources, including email analytics, raw data exports, reviews, webhooks, unsubscribers, and webhook events. The API uses an API key for authentication and supports pagination and incremental data synchronization.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "access_token",
            "api_key": dlt.secrets["access_token"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "email_analytics",
            "endpoint": {
                "path": "/analytics/v1/emails/{app_key}/emails_sent",
                "data_selector": "date_series_points"
            }
        },
        {
            "name": "raw_data",
            "endpoint": {
                "path": "/analytics/v1/emails/{app_key}/export/raw_data",
                "data_selector": "records"
            }
        },
        {
            "name": "reviews",
            "endpoint": {
                "path": "/v1/apps/{app_key}/reviews",
                "data_selector": "reviews",
                "paginator": {
                    "type": "offset",
                    "limit": 1,
                    "offset_param": "page",
                    "limit_param": "count"
                }
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/apps/{app_key}/webhooks",
                "data_selector": ["response", "webhooks"]
            }
        },
        {
            "name": "unsubscribers",
            "endpoint": {
                "path": "/apps/{app_key}/unsubscribers",
                "data_selector": ["response", "unsubscribers"],
                "paginator": {
                    "type": "offset",
                    "limit": 1,
                    "offset_param": "page",
                    "limit_param": "count"
                }
            }
        },
        {
            "name": "webhook_events",
            "endpoint": {
                "path": "/webhook_events",
                "data_selector": ["response", "webhook_events"]
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Secrets**: 
  - `access_token`: The API key used for authentication, stored securely in dltHub secrets.
- **Location**: Query parameter

## Resources

### Email Analytics
- **Endpoint Path**: `/analytics/v1/emails/{app_key}/emails_sent`
- **Data Selector**: `date_series_points`

### Raw Data
- **Endpoint Path**: `/analytics/v1/emails/{app_key}/export/raw_data`
- **Data Selector**: `records`

### Reviews
- **Endpoint Path**: `/v1/apps/{app_key}/reviews`
- **Data Selector**: `reviews`
- **Pagination**: Offset-based with parameters `page` and `count`

### Webhooks
- **Endpoint Path**: `/apps/{app_key}/webhooks`
- **Data Selector**: `["response", "webhooks"]`

### Unsubscribers
- **Endpoint Path**: `/apps/{app_key}/unsubscribers`
- **Data Selector**: `["response", "unsubscribers"]`
- **Pagination**: Offset-based with parameters `page` and `count`

### Webhook Events
- **Endpoint Path**: `/webhook_events`
- **Data Selector**: `["response", "webhook_events"]`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Email Analytics Schema
- **Fields**: `since`, `until`, `values.done`

### Raw Data Schema
- **Fields**: `arrived_early_timestamp`, `clicked_through_timestamp`, `content_creation_timestamp`, `coupon_code`, `email_address`, `email_sent_timestamp`, `email_type`, `failed_timestamp`, `invalid_address_timestamp`, `marked_spam_timestamp`, `opened_timestamp`, `order_id`, `order_timestamp`, `platform`, `product_id`, `reminder_num`, `review_type`, `sku`, `trr_bundle_id`, `trr_bundle_subject`, `unsubscribed_timestamp`

### Reviews Schema
- **Fields**: `archived`, `content`, `created_at`, `deleted`, `email`, `escalated`, `id`, `incentive_type`, `is_incentivized`, `name`, `reviewer_type`, `score`, `sentiment`, `sku`, `title`, `updated_at`, `votes_down`, `votes_up`

### Webhooks Schema
- **Fields**: `version`, `id`, `url`, `webhook_event_id`, `webhook_event_name`

### Unsubscribers Schema
- **Fields**: `email_type_id`, `id`, `unsubscribed_by_name`, `user_email`

### Webhook Events Schema
- **Fields**: `description`, `name`

This configuration provides a comprehensive setup for accessing and synchronizing data from a third-party analytics API using dltHub's REST API source format.