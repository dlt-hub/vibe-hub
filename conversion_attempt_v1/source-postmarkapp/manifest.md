# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, each with specific endpoints and pagination strategies. The API requires authentication via API keys, and the configuration includes error handling and schema definitions for each resource.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
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
            "name": "deliverystats",
            "endpoint": {
                "path": "/v1/deliverystats",
                "data_selector": "Bounces",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "message_streams",
            "endpoint": {
                "path": "/v1/message-streams",
                "data_selector": "MessageStreams",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "domains",
            "endpoint": {
                "path": "/v1/domains",
                "data_selector": "Domains",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "messages",
            "endpoint": {
                "path": "/v1/messages/outbound",
                "data_selector": "Messages",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "bounces",
            "endpoint": {
                "path": "/v1/bounces",
                "data_selector": "Bounces",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "servers",
            "endpoint": {
                "path": "/v1/servers",
                "data_selector": "Servers",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: 
  - `api_key`: The API key is stored securely and injected into the request headers.

## Resources

### Deliverystats
- **Endpoint Path**: `/v1/deliverystats`
- **Data Selector**: `Bounces`
- **Pagination**: Offset-based with a limit of 500 items per request.

### Message Streams
- **Endpoint Path**: `/v1/message-streams`
- **Data Selector**: `MessageStreams`
- **Pagination**: Offset-based with a limit of 500 items per request.

### Domains
- **Endpoint Path**: `/v1/domains`
- **Data Selector**: `Domains`
- **Pagination**: Offset-based with a limit of 500 items per request.

### Messages
- **Endpoint Path**: `/v1/messages/outbound`
- **Data Selector**: `Messages`
- **Pagination**: Offset-based with a limit of 500 items per request.

### Bounces
- **Endpoint Path**: `/v1/bounces`
- **Data Selector**: `Bounces`
- **Pagination**: Offset-based with a limit of 500 items per request.

### Servers
- **Endpoint Path**: `/v1/servers`
- **Data Selector**: `Servers`
- **Pagination**: Offset-based with a limit of 500 items per request.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Deliverystats
- **Fields**:
  - `Count`: Integer, total count of delivered items.
  - `Name`: String, name of the delivery status type.

### Message Streams
- **Fields**:
  - `ArchivedAt`: Null, timestamp when archived.
  - `CreatedAt`: String, creation timestamp.
  - `Description`: String, description of the stream.
  - `ID`: String, unique identifier.
  - `MessageStreamType`: String, type of stream.
  - `Name`: String, name of the stream.
  - `ServerID`: Integer, server identifier.
  - `SubscriptionManagementConfiguration`: Object, subscription settings.

### Domains
- **Fields**:
  - `DKIMVerified`: Boolean, DKIM verification status.
  - `ID`: Integer, domain identifier.
  - `Name`: String, domain name.
  - `ReturnPathDomainVerified`: Boolean, return path verification status.
  - `SPFVerified`: Boolean, SPF verification status.
  - `WeakDKIM`: Boolean, weak DKIM status.

### Messages
- **Fields**:
  - `Attachments`: Array, list of attachments.
  - `Bcc`: Array, BCC email addresses.
  - `Cc`: Array, CC email addresses.
  - `From`: String, sender's email.
  - `MessageID`: String, message identifier.
  - `MessageStream`: String, stream identifier.
  - `Metadata`: Object, additional metadata.
  - `ReceivedAt`: String, received timestamp.
  - `Recipients`: Array, recipient email addresses.
  - `Sandboxed`: Boolean, sandbox mode status.
  - `Status`: String, message status.
  - `Subject`: String, subject line.
  - `Tag`: String, message tag.
  - `To`: Array, recipient details.
  - `TrackLinks`: String, link tracking status.
  - `TrackOpens`: Boolean, open tracking status.

### Bounces
- **Fields**:
  - `BouncedAt`: String, bounce timestamp.
  - `CanActivate`: Boolean, reactivation status.
  - `Description`: String, bounce reason.
  - `Details`: String, additional bounce details.
  - `DumpAvailable`: Boolean, dump availability.
  - `Email`: String, bounced email address.
  - `From`: String, sender's email.
  - `ID`: Integer, bounce identifier.
  - `Inactive`: Boolean, inactive status.
  - `MessageID`: String, original message identifier.
  - `MessageStream`: String, stream identifier.
  - `Name`: String, recipient's name.
  - `ServerID`: Integer, server identifier.
  - `Subject`: String, original email subject.
  - `Tag`: String, message tag.
  - `Type`: String, bounce type.
  - `TypeCode`: Integer, bounce type code.

### Servers
- **Fields**:
  - `ApiTokens`: Array, list of API tokens.
  - `BounceHookUrl`: String, bounce notification URL.
  - `ClickHookUrl`: String, click event URL.
  - `Color`: String, server color.
  - `DeliveryHookUrl`: String, delivery event URL.
  - `DeliveryType`: String, delivery mechanism.
  - `EnableSmtpApiErrorHooks`: Boolean, SMTP API error hooks status.
  - `ID`: Integer, server identifier.
  - `InboundAddress`: String, inbound email address.
  - `InboundDomain`: String, inbound domain.
  - `InboundHash`: String, inbound hash code.
  - `InboundHookUrl`: String, inbound notification URL.
  - `InboundSpamThreshold`: Integer, spam threshold.
  - `IncludeBounceContentInHook`: Boolean, bounce content inclusion.
  - `Name`: String, server name.
  - `OpenHookUrl`: String, open event URL.
  - `PostFirstOpenOnly`: Boolean, first open tracking status.
  - `RawEmailEnabled`: Boolean, raw email content status.
  - `ServerLink`: String, server details link.
  - `SmtpApiActivated`: Boolean, SMTP API activation status.
  - `TrackLinks`: String, link tracking status.
  - `TrackOpens`: Boolean, open tracking status.