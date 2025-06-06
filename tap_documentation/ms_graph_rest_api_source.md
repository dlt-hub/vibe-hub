# Ms Graph REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for the Microsoft Graph API using the `tap-ms-graph` connector. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://graph.microsoft.com/`
- **API Version**: `v1.0` (default) or `beta`

### Authentication
- **Type**: `oauth2`
- **OAuth2 Configuration**:
  - **Auth URL**: `https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token`
  - **Scopes**: `https://graph.microsoft.com/.default`
  - **Client ID**: Required
  - **Client Secret**: Required
  - **Tenant**: Required

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Data Selector**: `$.value[*]`

#### Pagination Configuration
- **Type**: `json_link`
- **Next Page Token**: `@odata.nextLink`

#### Data Extraction
- **JSONPath**: `$.value[*]`
- **Key Fields**: `id`

### Groups Endpoint
- **Path**: `/groups`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Data Selector**: `$.value[*]`

#### Pagination Configuration
- **Type**: `json_link`
- **Next Page Token**: `@odata.nextLink`

#### Data Extraction
- **JSONPath**: `$.value[*]`
- **Key Fields**: `id`

### Group Members Endpoint
- **Path**: `/groups/{group_id}/members`
- **HTTP Method**: `GET`
- **Primary Key**: `group_id`, `id`
- **Data Selector**: `$.value[*]`

#### Pagination Configuration
- **Type**: `json_link`
- **Next Page Token**: `@odata.nextLink`

#### Data Extraction
- **JSONPath**: `$.value[*]`
- **Key Fields**: `id`

## 3. Incremental Data Loading

### Users Endpoint
- **Incremental Support**: Not explicitly supported; consider using a timestamp field like `createdDateTime` or `lastModifiedDateTime` for custom implementations.
- **Date/Time Format**: ISO 8601

### Groups Endpoint
- **Incremental Support**: Not explicitly supported; similar approach as Users.

## 4. Endpoint Dependencies

### Group Members Endpoint
- **Depends On**: Groups Endpoint
- **Parameter Mapping**: `group_id` from Groups Endpoint

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Varies; typically around 10 requests per second.
- **Burst Limits**: Implement retry logic with exponential backoff using the `Retry-After` header.

### Special Requirements
- **Custom Headers**: `ConsistencyLevel: eventual` for certain operations
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for 429 (Too Many Requests) and 5xx server errors.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://graph.microsoft.com/",
    "auth": {
        "type": "oauth2",
        "auth_url": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token",
        "scopes": "https://graph.microsoft.com/.default",
        "client_id": "your_client_id",
        "client_secret": "your_client_secret",
        "tenant": "your_tenant_id"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "$.value[*]",
        "primary_key": "id",
        "pagination": {
            "type": "json_link",
            "next_page_token": "@odata.nextLink"
        }
    },
    {
        "name": "groups",
        "path": "/groups",
        "method": "GET",
        "data_selector": "$.value[*]",
        "primary_key": "id",
        "pagination": {
            "type": "json_link",
            "next_page_token": "@odata.nextLink"
        }
    },
    {
        "name": "groupMembers",
        "path": "/groups/{group_id}/members",
        "method": "GET",
        "data_selector": "$.value[*]",
        "primary_key": ["group_id", "id"],
        "pagination": {
            "type": "json_link",
            "next_page_token": "@odata.nextLink"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "groupMembers",
        "depends_on": "groups",
        "param_mapping": {"group_id": "id"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for the Microsoft Graph API, ensuring efficient data extraction and handling of API-specific behaviors.

---
*dlt REST API Source Configuration Guide*