# Zohosprints REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Zoho Sprints

This document provides a comprehensive guide for configuring a dlt REST API source to integrate with the Zoho Sprints API. The configuration is designed to facilitate data extraction and pipeline setup using the `tap-zohosprints` connector.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://sprintsapi.zoho.com/zsapi`
- **Version Path**: Not applicable as the base URL includes the necessary path.

### Authentication
- **Type**: `oauth2`
- **OAuth2 Configuration**:
  - **Token URL**: `https://accounts.zoho.com/oauth/v2/token`
  - **Required Scopes**: Not explicitly mentioned, but typically includes access to sprints data.
  - **Parameters**:
    - `client_id`: Your Zoho client ID.
    - `client_secret`: Your Zoho client secret.
    - `refresh_token`: Your Zoho refresh token.
    - `redirect_uri`: Typically `http://localhost` (required by API but not used).

## 2. Available Endpoints

### Teams Endpoint
- **Path**: `/teams/`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON with team details.
- **Pagination**: Not applicable.

### Meta Projects Endpoint
- **Path**: `/team/{myTeamId}/projects/?action=allprojects`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON with project metadata.
- **Pagination**: Not implemented.

### Projects Endpoint
- **Path**: `/team/{myTeamId}/projects/{projectId}/?action=details`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON with detailed project information.
- **Pagination**: Not applicable.

### Tags Endpoint
- **Path**: `/team/{myTeamId}/tags/?action=data&index=1&range=1000`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON with tag details.
- **Pagination**: Not applicable.

### Epics Endpoint
- **Path**: `/team/{myTeamId}/projects/{projectId}/epic/?action=data`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON with epic details.
- **Pagination**: Not applicable.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: Not explicitly supported in the current configuration.
- **Date/Timestamp Field**: Not specified.
- **Expected Format**: Not applicable.
- **Recommended Initial Values**: Not applicable.

## 4. Endpoint Dependencies

### Resource Relationships
- **Teams**: Acts as a parent for projects and tags.
- **Projects**: Acts as a parent for epics, sprints, and users.
- **Sprints**: Acts as a parent for sprint items and users.

### Mapping Identifiers
- **Teams to Projects**: `myTeamId`
- **Projects to Epics/Sprints**: `projectId`
- **Sprints to Items**: `sprintId`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: 30 requests per 60 seconds.
- **Burst Limits**: Implement a delay if the limit is reached.

### Special Requirements
- **Custom Headers**: Include `User-Agent` if specified in the configuration.
- **Response Format**: JSON.
- **Error Handling**: Handle 400-499 as fatal errors and 500-599 as retriable errors.
- **Data Type Specifications**: Ensure correct data types as per JSON schema definitions.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://sprintsapi.zoho.com/zsapi",
    "auth": {
        "type": "oauth2",
        "token_url": "https://accounts.zoho.com/oauth/v2/token",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "refresh_token": "<your_refresh_token>",
        "redirect_uri": "http://localhost"
    }
}

ENDPOINTS = [
    {
        "name": "teams",
        "path": "/teams/",
        "method": "GET",
        "data_selector": "$",
        "primary_key": "ownerTeamIds"
    },
    {
        "name": "meta_projects",
        "path": "/team/{myTeamId}/projects/?action=allprojects",
        "method": "GET",
        "data_selector": "$",
        "primary_key": "projectId"
    },
    {
        "name": "projects",
        "path": "/team/{myTeamId}/projects/{projectId}/?action=details",
        "method": "GET",
        "data_selector": "$",
        "primary_key": "projectId"
    },
    {
        "name": "tags",
        "path": "/team/{myTeamId}/tags/?action=data&index=1&range=1000",
        "method": "GET",
        "data_selector": "$",
        "primary_key": "tagId"
    },
    {
        "name": "epics",
        "path": "/team/{myTeamId}/projects/{projectId}/epic/?action=data",
        "method": "GET",
        "data_selector": "$",
        "primary_key": "epicId"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "meta_projects",
        "depends_on": "teams",
        "param_mapping": {"myTeamId": "portals[0].zsoid"}
    },
    {
        "endpoint": "projects",
        "depends_on": "meta_projects",
        "param_mapping": {"projectId": "projectId"}
    },
    {
        "endpoint": "epics",
        "depends_on": "projects",
        "param_mapping": {"projectId": "projectId"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for Zoho Sprints, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*