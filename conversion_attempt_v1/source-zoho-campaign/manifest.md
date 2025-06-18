# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for seamless extraction and synchronization of various data resources, such as campaign data, subscriber information, and mailing lists, into your data workflows. This setup is designed to automate the collection of marketing insights and analytics.

# Configuration Example

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
            "refresh_token": dlt.secrets["refresh_token"],
            "token_url": "https://accounts.example.com/oauth/v2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "recent_campaigns",
            "endpoint": {
                "path": "/v1/recentcampaigns",
                "data_selector": "recent_campaigns",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "fromindex"
                }
            }
        },
        {
            "name": "subscribers",
            "endpoint": {
                "path": "/v1/getlistsubscribers",
                "data_selector": "list_of_details",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "fromindex"
                }
            }
        },
        {
            "name": "campaign_recipients",
            "endpoint": {
                "path": "/v1/getcampaignrecipientsdata",
                "data_selector": "list_of_details",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "fromindex"
                }
            }
        },
        {
            "name": "campaign_reports",
            "endpoint": {
                "path": "/v1/campaignreports",
                "data_selector": "campaign-reports"
            }
        },
        {
            "name": "recent_sent_campaigns",
            "endpoint": {
                "path": "/v1/recentsentcampaigns",
                "data_selector": "recent_sent_campaigns"
            }
        },
        {
            "name": "mailing_lists",
            "endpoint": {
                "path": "/v1/getmailinglists",
                "data_selector": "list_of_details",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "fromindex"
                }
            }
        },
        {
            "name": "totalcontacts",
            "endpoint": {
                "path": "/v1/listsubscriberscount",
                "data_selector": []
            }
        },
        {
            "name": "topics",
            "endpoint": {
                "path": "/v1/topics",
                "data_selector": "topicDetails"
            }
        },
        {
            "name": "campaign_details",
            "endpoint": {
                "path": "/v1/getcampaigndetails",
                "data_selector": "campaign-details"
            }
        },
        {
            "name": "all_tags",
            "endpoint": {
                "path": "/v1/tag/getalltags",
                "data_selector": "tags"
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Secrets**:
  - `client_id`: Your client ID for the API.
  - `client_secret`: Your client secret for the API.
  - `refresh_token`: The refresh token for obtaining new access tokens.
- **Token URL**: `https://accounts.example.com/oauth/v2/token`

# Resources

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized paths for each resource.
- **Pagination Strategy**: Offset-based pagination with parameters `fromindex` and `range`.
- **Data Selector**: Field paths for extracting data from the API response.

# Error Handling

- **Retry Logic**: Implements a backoff strategy with a constant delay of 1800 seconds.
- **HTTP Status Codes**: Retries on common server error codes such as 429, 500, 502, 503, and 504.
- **Fallback Behavior**: Generalized error handling without specific vendor logic.

# Schema

- **Fields**: Neutral field names are used, such as `campaign_key`, `contact_email`, and `listunino`.
- **Types**: Consistent with the original YAML schema, using types like `string` and `null`.
- **Nullability**: Fields can be nullable, as indicated in the schema.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. It abstracts away any proprietary details while maintaining the core functionality and structure required for data synchronization.