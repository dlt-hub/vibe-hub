# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data retrieval operations, including organization lookup, follower statistics, share statistics, and more. The API supports OAuth2.0 and Bearer token authentication methods.

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
            "refresh_token": dlt.secrets["refresh_token"],
            "token_url": "https://api.example.com/oauth/v2/accessToken"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "organization_lookup",
            "endpoint": {
                "path": "/v1/organizations/{org_id}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "follower_statistics",
            "endpoint": {
                "path": "/v1/organizationalEntityFollowerStatistics",
                "data_selector": "elements",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "share_statistics",
            "endpoint": {
                "path": "/v1/organizationalEntityShareStatistics",
                "data_selector": "elements",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "total_follower_count",
            "endpoint": {
                "path": "/v1/networkSizes/urn:li:organization:{org_id}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2.0 or Bearer
- **Secrets**:
  - `client_id`: The client ID for OAuth2.0 authentication.
  - `client_secret`: The client secret for OAuth2.0 authentication.
  - `refresh_token`: The refresh token for OAuth2.0 authentication.
  - `api_token`: The access token for Bearer authentication.

## Resources

### Organization Lookup

- **Endpoint Path**: `/v1/organizations/{org_id}`
- **Data Selector**: `data`
- **Pagination**: Single page

### Follower Statistics

- **Endpoint Path**: `/v1/organizationalEntityFollowerStatistics`
- **Data Selector**: `elements`
- **Pagination**: Single page

### Share Statistics

- **Endpoint Path**: `/v1/organizationalEntityShareStatistics`
- **Data Selector**: `elements`
- **Pagination**: Single page

### Total Follower Count

- **Endpoint Path**: `/v1/networkSizes/urn:li:organization:{org_id}`
- **Data Selector**: `data`
- **Pagination**: Single page

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429
  - Maximum retries: 3
  - Backoff factor: 2

- **Known HTTP Response Codes**:
  - 429: Rate limit exceeded

## Schema

- **Fields**:
  - `alternativeNames`: Array or null
  - `autoCreated`: Boolean or null
  - `created`: Object with `actor` (string or null) and `time` (number or null)
  - `defaultLocale`: Object with `country` (string or null) and `language` (string or null)
  - `groups`: Array or null
  - `id`: Number or null
  - `lastModified`: Object with `actor` (string or null) and `time` (number or null)
  - `localizedName`: String or null
  - `localizedSpecialties`: Array or null
  - `locations`: Array or null
  - `name`: Object with `localized` (object or null) and `preferredLocale` (object or null)
  - `organizationType`: String or null
  - `primaryOrganizationType`: String or null
  - `specialties`: Array or null
  - `vanityName`: String or null
  - `versionTag`: String or null

This configuration provides a comprehensive setup for accessing and retrieving data from a third-party analytics API using dltHub's REST API source.