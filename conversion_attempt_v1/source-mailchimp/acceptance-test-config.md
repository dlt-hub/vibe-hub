# Overview

This document provides a configuration guide for integrating with a third-party email marketing API using dltHub's REST API source. The integration supports both API key and OAuth2 authentication methods, allowing for flexible access to the API's resources. The configuration is designed to handle various data retrieval strategies, including incremental and full refresh syncs.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",  # or "oauth2"
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "email_campaigns",
            "endpoint": {
                "path": "/v1/email_campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "automations",
            "endpoint": {
                "path": "/v1/automations",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

The integration supports two types of authentication:

- **API Key Authentication**: 
  - Type: `api_key`
  - Key Name: `api_key`
  - Location: `query`
  - Secret: `dlt.secrets["api_key"]`

- **OAuth2 Authentication**:
  - Type: `oauth2`
  - Client ID: `dlt.secrets["client_id"]`
  - Client Secret: `dlt.secrets["client_secret"]`
  - Token URL: `https://api.example.com/oauth/token`

# Resources

- **Email Campaigns**
  - Endpoint Path: `/v1/email_campaigns`
  - Pagination: Offset-based with a limit of 100 records per request
  - Data Selector: `data`

- **Automations**
  - Endpoint Path: `/v1/automations`
  - Pagination: Offset-based with a limit of 100 records per request
  - Data Selector: `data`

# Error Handling

The configuration includes error handling strategies to manage API response errors:

- Retry on HTTP status codes: 429, 500, 502, 503, 504
- Maximum retries: 3
- Backoff factor: 2

# Schema

The schema for each resource is defined by the API's response structure. Field names are generalized to ensure vendor neutrality. For example, fields like `campaign_id` or `automation_id` are used instead of branded identifiers. The schema supports various data types and nullability as defined by the API's documentation.