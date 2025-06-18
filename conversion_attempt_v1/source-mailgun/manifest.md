# Overview

This document provides a configuration guide for integrating with a third-party email service API using dltHub's REST API source format. The integration supports two main resources: domains and events, each with its own endpoint and schema. The API allows for the retrieval of domain information and email event data, with support for pagination and authentication.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": "api",
            "password": dlt.secrets["private_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "domains",
            "endpoint": {
                "path": "/v1/domains",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "from",
                    "cursor_path": "paging.next"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Username**: `api`
- **Password**: Retrieved from `dlt.secrets["private_key"]`

## Resources

### Domains

- **Resource Name**: domains
- **Endpoint Path**: `/v1/domains`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 1000
  - **Offset Parameter**: `skip`
- **Data Selector**: `items`

### Events

- **Resource Name**: events
- **Endpoint Path**: `/v1/events`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor
  - **Cursor Parameter**: `from`
  - **Cursor Path**: `paging.next`
- **Data Selector**: `items`

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Domains Schema

- **type**: Type of the domain (e.g., custom, sandbox).
- **created_at**: The date and time when the domain was created.
- **dkim_key_size**: The size of the DKIM key associated with the domain.
- **force_dkim_authority**: Indicates whether DKIM authority needs to be enforced.
- **id**: The unique identifier for the domain.
- **ips**: List of IP addresses associated with the domain.
- **is_disabled**: Specifies if the domain is disabled.
- **name**: The name of the domain.
- **pool**: The pool to which the domain belongs.
- **require_tls**: Specifies if TLS is required for emails sent from this domain.
- **skip_verification**: Indicates whether email verification should be skipped.
- **smtp_login**: SMTP login credentials for the domain.
- **smtp_password**: SMTP password associated with the domain.
- **spam_action**: Action to take for emails marked as spam.
- **state**: The current state of the domain.
- **web_prefix**: Prefix for webhooks related to the domain.
- **web_scheme**: The scheme for webhook URLs (e.g., https).
- **wildcard**: Indicates if the domain allows wildcard addresses.

### Events Schema

- **campaigns**: List of campaigns that the email is associated with.
- **client-info**: Information about the client device used to open the email.
- **delivery-status**: Delivery status of the email.
- **envelop**: Envelop information of the email.
- **event**: Type of event (e.g., opened, clicked).
- **flags**: Flags associated with the email.
- **geolocation**: Geolocation information of the recipient.
- **id**: ID of the email event.
- **ip**: IP address of the recipient.
- **log-level**: Log level information.
- **message**: Message details of the email event.
- **method**: Method used for the event (e.g., POST, GET).
- **routes**: List of routes for the email event.
- **storage**: Storage details of the email event.
- **reason**: Reason for the event.
- **reject**: Details when the email is rejected.
- **recipient**: Email address of the recipient.
- **recipient-domain**: Recipient's domain name.
- **severity**: Severity of the event.
- **tags**: Tags associated with the email event.
- **timestamp**: Timestamp of the event.
- **user-variables**: User-defined variables associated with the event.