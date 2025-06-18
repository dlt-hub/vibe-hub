# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data retrieval operations, including campaigns, ad groups, keywords, and daily reports. The configuration is designed to handle authentication, pagination, error handling, and data transformation in a vendor-neutral manner.

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
            "token_url": dlt.secrets.get("token_refresh_endpoint", "https://example.com/auth/oauth2/token")
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
                "path": "/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [500, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "adgroups",
            "endpoint": {
                "path": "/campaigns/{campaign_id}/adgroups",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [500, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "keywords",
            "endpoint": {
                "path": "/campaigns/{campaign_id}/adgroups/{adgroup_id}/keywords",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [500, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "campaigns_report_daily",
            "endpoint": {
                "path": "/reports/campaigns",
                "method": "POST",
                "data_selector": "data.reportingDataResponse.row",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "selector.pagination.offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [500, 429],
                    "max_retries": 10,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: Retrieved from `dlt.secrets["client_id"]`
- **Client Secret**: Retrieved from `dlt.secrets["client_secret"]`
- **Token URL**: Default is `https://example.com/auth/oauth2/token`, can be overridden by `dlt.secrets["token_refresh_endpoint"]`

# Resources

## Campaigns
- **Endpoint Path**: `/campaigns`
- **Primary Key**: `id`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

## Ad Groups
- **Endpoint Path**: `/campaigns/{campaign_id}/adgroups`
- **Primary Key**: `id`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

## Keywords
- **Endpoint Path**: `/campaigns/{campaign_id}/adgroups/{adgroup_id}/keywords`
- **Primary Key**: `id`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

## Campaigns Report Daily
- **Endpoint Path**: `/reports/campaigns`
- **HTTP Method**: POST
- **Primary Key**: `id`
- **Pagination**: Offset-based with `selector.pagination.offset` parameter
- **Data Selector**: `data.reportingDataResponse.row`

# Error Handling

- **Retry on Status Codes**: 500, 429
- **Max Retries**: 3 for most resources, 10 for report resources
- **Backoff Factor**: 2

# Schema

- **Campaigns**: Includes fields like `id`, `name`, `status`, etc.
- **Ad Groups**: Includes fields like `id`, `campaignId`, `name`, etc.
- **Keywords**: Includes fields like `id`, `adGroupId`, `text`, etc.
- **Reports**: Includes fields like `date`, `countryorregion`, `granularity`, etc.

This configuration provides a comprehensive setup for interacting with a third-party analytics API, ensuring robust data retrieval and error handling.