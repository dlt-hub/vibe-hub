# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows seamless data synchronization from the analytics API into data warehouses or BI tools. It automates OAuth authentication and ensures reliable data transfer, enabling businesses to streamline analytics workflows and gain deeper insights efficiently.

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
            "token_url": "https://accounts.example.com/oauth/v2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data.users"
            }
        },
        {
            "name": "workspaces",
            "endpoint": {
                "path": "/v1/workspaces",
                "data_selector": "data.ownedWorkspaces"
            }
        },
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v1/orgs",
                "data_selector": "data.orgs"
            }
        },
        {
            "name": "views",
            "endpoint": {
                "path": "/v1/workspaces/{workspace}/views",
                "data_selector": "data.views"
            }
        },
        {
            "name": "dashboards",
            "endpoint": {
                "path": "/v1/dashboards",
                "data_selector": "data.ownedViews"
            }
        },
        {
            "name": "trash",
            "endpoint": {
                "path": "/v1/workspaces/{workspace}/trash",
                "data_selector": "data.views"
            }
        },
        {
            "name": "workspace_users",
            "endpoint": {
                "path": "/v1/workspaces/{workspace}/users",
                "data_selector": "data.users"
            }
        },
        {
            "name": "folders",
            "endpoint": {
                "path": "/v1/workspaces/{workspace}/folders",
                "data_selector": "data.folders"
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets or Config Values**:
  - `client_id`
  - `client_secret`
  - `refresh_token`
  - `token_url`: "https://accounts.example.com/oauth/v2/token"

## Resources

### Users
- **Endpoint Path**: `/v1/users`
- **Data Selector**: `data.users`
- **Primary Key**: `emailId`

### Workspaces
- **Endpoint Path**: `/v1/workspaces`
- **Data Selector**: `data.ownedWorkspaces`
- **Primary Key**: `workspaceId`

### Organizations
- **Endpoint Path**: `/v1/orgs`
- **Data Selector**: `data.orgs`
- **Primary Key**: `orgId`

### Views
- **Endpoint Path**: `/v1/workspaces/{workspace}/views`
- **Data Selector**: `data.views`
- **Primary Key**: `viewId`

### Dashboards
- **Endpoint Path**: `/v1/dashboards`
- **Data Selector**: `data.ownedViews`
- **Primary Key**: `viewId`

### Trash
- **Endpoint Path**: `/v1/workspaces/{workspace}/trash`
- **Data Selector**: `data.views`
- **Primary Key**: `viewId`

### Workspace Users
- **Endpoint Path**: `/v1/workspaces/{workspace}/users`
- **Data Selector**: `data.users`
- **Primary Key**: `emailId`

### Folders
- **Endpoint Path**: `/v1/workspaces/{workspace}/folders`
- **Data Selector**: `data.folders`
- **Primary Key**: `folderId`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Users
- **Fields**:
  - `emailId`: string
  - `role`: string or null
  - `status`: boolean or null

### Workspaces
- **Fields**:
  - `workspaceId`: string
  - `workspaceName`: string or null
  - `createdBy`: string or null
  - `createdTime`: string or null

### Organizations
- **Fields**:
  - `orgId`: string
  - `orgName`: string or null
  - `createdBy`: string or null
  - `numberOfWorkspaces`: number or null

### Views
- **Fields**:
  - `viewId`: string
  - `viewName`: string or null
  - `createdBy`: string or null
  - `createdTime`: string or null

### Dashboards
- **Fields**:
  - `viewId`: string
  - `viewName`: string or null
  - `createdBy`: string or null
  - `createdTime`: string or null

### Trash
- **Fields**:
  - `viewId`: string
  - `viewName`: string or null
  - `deletedBy`: string or null
  - `deletedTime`: string or null

### Workspace Users
- **Fields**:
  - `emailId`: string
  - `role`: string or null
  - `status`: boolean or null

### Folders
- **Fields**:
  - `folderId`: string
  - `folderName`: string or null
  - `folderDesc`: string or null

This configuration guide provides a comprehensive overview of how to set up and use the dltHub REST API source for integrating with a third-party analytics API. It includes details on authentication, resource configuration, error handling, and schema definitions, ensuring a smooth and efficient data synchronization process.