# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports OAuth2 authentication and paginated data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/v2",
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
            "name": "clients",
            "endpoint": {
                "path": "/clients",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "per_page",
                    "page_param": "page",
                    "limit": 50
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "per_page",
                    "page_param": "page",
                    "limit": 50
                }
            }
        },
        {
            "name": "organizations",
            "endpoint": {
                "path": "/organizations",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "per_page",
                    "page_param": "page",
                    "limit": 50
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Client ID**: `dlt.secrets["client_id"]`
- **Client Secret**: `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/oauth/token`

## Resources

### Clients

- **Endpoint Path**: `/clients`
- **Pagination Strategy**: Page Number
- **Data Selector**: `data`
- **Primary Key**: `client_id`

### Users

- **Endpoint Path**: `/users`
- **Pagination Strategy**: Page Number
- **Data Selector**: `data`
- **Primary Key**: `user_id`

### Organizations

- **Endpoint Path**: `/organizations`
- **Pagination Strategy**: Page Number
- **Data Selector**: `data`
- **Primary Key**: `id`

## Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Clients

- **client_id**: string
- **tenant**: string
- **owners**: array of strings
- **is_token_endpoint_ip_header_trusted**: boolean
- **cross_origin_auth**: boolean
- **callback_url_template**: boolean
- **name**: string
- **description**: string
- **global**: boolean
- **client_secret**: string
- **app_type**: string
- **logo_uri**: string
- **is_first_party**: boolean
- **oidc_conformant**: boolean
- **callbacks**: array of strings
- **allowed_origins**: array of strings
- **web_origins**: array of strings
- **client_aliases**: array of strings
- **allowed_clients**: array of strings
- **allowed_logout_urls**: array of strings
- **oidc_backchannel_logout**: object
- **grant_types**: array of strings
- **jwt_configuration**: object
- **signing_keys**: array of objects
- **encryption_key**: object
- **sso**: boolean
- **sso_disabled**: boolean
- **cross_origin_authentication**: boolean
- **cross_origin_loc**: string
- **custom_login_page_on**: boolean
- **custom_login_page**: string
- **custom_login_page_preview**: string
- **form_template**: string
- **addons**: object
- **token_endpoint_auth_method**: string
- **client_metadata**: object
- **mobile**: object
- **initiate_login_uri**: string
- **native_social_login**: object
- **refresh_token**: object
- **organization_usage**: string
- **organization_require_behavior**: string
- **client_authentication_methods**: object

### Users

- **user_id**: string
- **username**: string
- **email**: string
- **email_verified**: boolean
- **phone_number**: string
- **phone_verified**: boolean
- **created_at**: date-time
- **updated_at**: date-time
- **identities**: array of objects
- **app_metadata**: object
- **user_metadata**: object
- **picture**: string
- **name**: string
- **nickname**: string
- **multifactor**: array of strings
- **last_ip**: string
- **logins_count**: integer
- **blocked**: boolean
- **last_login**: string
- **given_name**: string
- **family_name**: string

### Organizations

- **id**: string
- **name**: string
- **display_name**: string
- **branding**: object
- **metadata**: object

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source.