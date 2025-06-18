# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for data ingestion from various resources provided by the API, such as accounts, campaigns, and audience data. The API requires OAuth2 authentication and supports multiple endpoints for data retrieval.

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
            "token_url": "https://api.example.com/oauth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "accounts",
            "endpoint": {
                "path": "/v1/users/current/allowed-accounts",
                "data_selector": "results"
            }
        },
        {
            "name": "audiences",
            "endpoint": {
                "path": "/v1/{account_id}/combined_audiences/resources/audiences",
                "data_selector": "results"
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/{account_id}/campaigns/",
                "data_selector": "results"
            }
        },
        {
            "name": "motion_ads",
            "endpoint": {
                "path": "/v1/{account_id}/campaigns/{parent_id}/performance-video/items/",
                "data_selector": "results"
            }
        },
        {
            "name": "audience_rules",
            "endpoint": {
                "path": "/v1/{account_id}/universal_pixel/custom_audience_rule",
                "data_selector": "results"
            }
        },
        {
            "name": "campaign_items",
            "endpoint": {
                "path": "/v1/{account_id}/campaigns/{parent_id}/items/",
                "data_selector": "results"
            }
        },
        {
            "name": "conversion_rules",
            "endpoint": {
                "path": "/v1/{account_id}/universal_pixel/conversion_rule",
                "data_selector": "results"
            }
        }
    ]
})
```

# Authentication

The API uses OAuth2 authentication. The following credentials are required:

- `client_id`: Your client ID for the API.
- `client_secret`: Your client secret for the API.
- `token_url`: The URL to obtain the access token.

These credentials should be stored securely and accessed via `dlt.secrets`.

# Resources

Each resource corresponds to a specific endpoint in the API:

- **Accounts**: Retrieves account information.
  - Path: `/v1/users/current/allowed-accounts`
  - Data Selector: `results`

- **Audiences**: Retrieves audience data.
  - Path: `/v1/{account_id}/combined_audiences/resources/audiences`
  - Data Selector: `results`

- **Campaigns**: Retrieves campaign data.
  - Path: `/v1/{account_id}/campaigns/`
  - Data Selector: `results`

- **Motion Ads**: Retrieves motion ad data.
  - Path: `/v1/{account_id}/campaigns/{parent_id}/performance-video/items/`
  - Data Selector: `results`

- **Audience Rules**: Retrieves audience rules.
  - Path: `/v1/{account_id}/universal_pixel/custom_audience_rule`
  - Data Selector: `results`

- **Campaign Items**: Retrieves campaign items.
  - Path: `/v1/{account_id}/campaigns/{parent_id}/items/`
  - Data Selector: `results`

- **Conversion Rules**: Retrieves conversion rules.
  - Path: `/v1/{account_id}/universal_pixel/conversion_rule`
  - Data Selector: `results`

# Error Handling

The configuration does not specify error handling, but it is recommended to implement retry logic for HTTP status codes such as 429 (Too Many Requests) and 500 (Internal Server Error). A typical error handler might include:

```python
"error_handler": {
    "retry_on_status_codes": [429, 500, 502, 503, 504],
    "max_retries": 3,
    "backoff_factor": 2
}
```

# Schema

The schema for each resource is defined with neutral field names. For example, the `accounts` resource includes fields such as `id`, `name`, `country`, and `currency`. Each field's type and nullability are specified, ensuring data consistency and integrity.