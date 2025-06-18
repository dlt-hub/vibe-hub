# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration allows for seamless data synchronization from various resources, enabling effective data analysis and management in a centralized location.

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
            "token_url": "https://api.example.com/oauth/access_token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "mailing_lists",
            "endpoint": {
                "path": "/addressbooks",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/templates",
                "data_selector": "data"
            }
        },
        {
            "name": "senders",
            "endpoint": {
                "path": "/senders",
                "data_selector": "data"
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/v2/email-service/webhook",
                "data_selector": "data"
            }
        },
        {
            "name": "balance_details",
            "endpoint": {
                "path": "/user/balance/detail",
                "data_selector": "data"
            }
        },
        {
            "name": "balance",
            "endpoint": {
                "path": "/balance",
                "data_selector": "data"
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Client ID**: `dlt.secrets["client_id"]`
- **Client Secret**: `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/oauth/access_token`

## Resources

### Mailing Lists
- **Endpoint Path**: `/addressbooks`
- **Pagination**: Offset with `limit` of 100 and `offset_param` as "offset"
- **Primary Key**: `id`

### Campaigns
- **Endpoint Path**: `/campaigns`
- **Pagination**: Offset with `limit` of 100 and `offset_param` as "offset"
- **Primary Key**: `id`

### Templates
- **Endpoint Path**: `/templates`
- **Primary Key**: `id`

### Senders
- **Endpoint Path**: `/senders`
- **Primary Key**: `email`

### Webhooks
- **Endpoint Path**: `/v2/email-service/webhook`
- **Primary Key**: `id`

### Balance Details
- **Endpoint Path**: `/user/balance/detail`

### Balance
- **Endpoint Path**: `/balance`
- **Primary Key**: `currency`

## Error Handling

- **Retry Logic**: Implement retry on HTTP status codes [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Mailing Lists
- **Fields**: `active_email_qty`, `active_phones_quantity`, `all_email_qty`, `creationdate`, `exc_phones_quantity`, `id`, `inactive_email_qty`, `name`, `new_phones_quantity`, `status`, `status_explain`
- **Primary Key**: `id`

### Campaigns
- **Fields**: `all_email_qty`, `company_price`, `id`, `is_sms`, `is_viber`, `message`, `name`, `overdraft_currency`, `overdraft_price`, `paid_email_qty`, `send_date`, `statistics`, `status`, `tariff_email_qty`
- **Primary Key**: `id`

### Templates
- **Fields**: `category`, `category_info`, `created`, `full_description`, `id`, `is_structure`, `lang`, `name`, `name_slug`, `owner`, `preview`, `real_id`, `tags`
- **Primary Key**: `id`

### Senders
- **Fields**: `email`, `is_allowed_for_smtp`, `name`, `status`
- **Primary Key**: `email`

### Webhooks
- **Fields**: `action`, `id`, `url`, `user_id`
- **Primary Key**: `id`

### Balance Details
- **Fields**: `balance`, `email`, `push`

### Balance
- **Fields**: `balance_currency`, `currency`, `datetime`
- **Primary Key**: `currency`

This configuration provides a comprehensive setup for integrating with a third-party analytics API using dltHub's REST API source, ensuring efficient data synchronization and management.