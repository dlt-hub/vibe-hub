# Brillium REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Brillium API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.brillium.com/v1/`
- This is the root URL for all API requests. Ensure to append the specific endpoint paths as needed.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Bearer {token}`
- The API requires an API key for authentication, which should be included in the request headers.

## 2. Available Endpoints

### Accounts Endpoint
- **Path**: `/Accounts`
- **HTTP Method**: `GET`
- **Primary Key**: `Id`
- **Data Selector**: `Accounts[*]`
- **Pagination**: 
  - **Type**: `page_number`
  - **Limit Parameter**: `pagesize`
  - **Page Parameter**: `page`
  - **Default Page Size**: 1000
- **Incremental**: 
  - **Cursor Path**: `DateModified`
  - **Parameter Name**: `since`
  - **Format**: `ISO 8601`

### EmailTemplates Endpoint
- **Path**: `/{email_templates_path}`
- **HTTP Method**: `GET`
- **Primary Key**: `Id`
- **Data Selector**: `EmailTemplates[*]`
- **Incremental**: 
  - **Cursor Path**: `DateModified`
  - **Parameter Name**: `since`
  - **Format**: `ISO 8601`

### Assessments Endpoint
- **Path**: `/{assessments_path}`
- **HTTP Method**: `GET`
- **Primary Key**: `Id`
- **Data Selector**: `Assessments[*]`
- **Incremental**: 
  - **Cursor Path**: `DateModified`
  - **Parameter Name**: `since`
  - **Format**: `ISO 8601`

## 3. Incremental Data Loading

- **Incremental Support**: Supported for endpoints with `DateModified` or `DateFinished` fields.
- **Date/Timestamp Field**: Use `DateModified` or `DateFinished` for tracking updates.
- **Format**: ISO 8601
- **Recommended Initial Values**: Use the `start_date` configuration parameter to set the initial sync date.

## 4. Endpoint Dependencies

### Resource Relationships
- **Accounts**: Parent stream for `EmailTemplates`, `Assessments`, and `Respondents`.
- **Assessments**: Parent stream for `QuestionGroups`, `Questions`, and `Respondents`.
- **Respondents**: Parent stream for `Results`.

### Mapping Identifiers
- Use the `Id` field from parent streams to map to child streams.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly defined; implement exponential backoff for retries.
- **Burst Limits**: Handle using retry mechanisms with backoff.

### Special Requirements
- **Custom Headers**: Include `User-Agent` and `Accept` headers as specified in the configuration.
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for transient errors and handle specific error messages as needed.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.brillium.com/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "accounts",
        "path": "/Accounts",
        "method": "GET",
        "data_selector": "Accounts",
        "primary_key": "Id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pagesize",
            "page_param": "page",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "DateModified",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "email_templates",
        "path": "/{email_templates_path}",
        "method": "GET",
        "data_selector": "EmailTemplates",
        "primary_key": "Id",
        "incremental": {
            "cursor_path": "DateModified",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "email_templates", 
        "depends_on": "accounts",
        "param_mapping": {"email_templates_path": "RelatedUris.EmailTemplates"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate and extract data from the Brillium API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*