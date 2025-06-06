# Aconex REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Aconex REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://au1.aconex.com/api`
- This is the root URL for all API requests.

**Authentication:**
- Type: `basic`
- Username and password are required for authentication.
- API Key: `X-Application-Key` must be included in the request headers.

```python
BASE_CONFIG = {
    "base_url": "https://au1.aconex.com/api",
    "auth": {
        "type": "basic",
        "username": "{username}",
        "password": "{password}",
        "headers": {
            "X-Application-Key": "{api_key}"
        }
    }
}
```

## 2. Available Endpoints

### Projects Endpoint

**Endpoint Details:**
- Path: `/projects`
- HTTP Method: `GET`
- Response Data Structure: JSON with `ProjectResults.SearchResults.Project` array

**Pagination Configuration:**
- Type: `page_number`
- Parameters: `page_number`, `page_size`
- Default Page Size: 500

**Data Extraction:**
- JSONPath: `ProjectResults.SearchResults.Project`
- Primary Key: `ProjectId`

### Documents Endpoint

**Endpoint Details:**
- Path: `/projects/{project_id}/register`
- HTTP Method: `GET`
- Required Query Parameters: `return_fields`, `search_query`

**Pagination Configuration:**
- Type: `page_number`
- Parameters: `page_number`, `page_size`
- Default Page Size: 500

**Data Extraction:**
- JSONPath: `RegisterSearch.Document`
- Primary Key: `DocumentId`

### Mail Endpoint

**Endpoint Details:**
- Path: `/projects/{project_id}/mail`
- HTTP Method: `GET`
- Required Query Parameters: `mail_box`, `return_fields`, `search_query`

**Pagination Configuration:**
- Type: `page_number`
- Parameters: `page_number`, `page_size`
- Default Page Size: 500

**Data Extraction:**
- JSONPath: `MailSearch.Mail`
- Primary Key: `MailId`

## 3. Incremental Data Loading

**Incremental Support:**
- Projects: `since` parameter based on `updated_at`
- Documents: `search_query` parameter based on `registered`
- Mail: `search_query` parameter based on `sentdate`

**Date/Time Format:**
- Format: `%Y%m%d` for filtering
- Initial Sync: Use current date minus a buffer period (e.g., 2 days)

## 4. Endpoint Dependencies

**Resource Relationships:**
- `documents` and `mail` depend on `projects`
- Mapping: `ProjectId` is used to fetch related documents and mail

```python
DEPENDENCIES = [
    {
        "endpoint": "documents",
        "depends_on": "projects",
        "param_mapping": {"project_id": "ProjectId"}
    },
    {
        "endpoint": "mail",
        "depends_on": "projects",
        "param_mapping": {"project_id": "ProjectId"}
    }
]
```

## 5. API Behavior & Limits

**Rate Limiting:**
- No explicit rate limits provided, but consider implementing a delay between requests to avoid throttling.

**Special Requirements:**
- Custom Headers: `X-Application-Key` for API key
- Response Format: XML responses are converted to JSON using `xmltodict`
- Error Handling: Check for HTTP status codes and handle exceptions accordingly

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://au1.aconex.com/api",
    "auth": {
        "type": "basic",
        "username": "{username}",
        "password": "{password}",
        "headers": {
            "X-Application-Key": "{api_key}"
        }
    }
}

ENDPOINTS = [
    {
        "name": "projects",
        "path": "/projects",
        "method": "GET",
        "data_selector": "ProjectResults.SearchResults.Project",
        "primary_key": "ProjectId",
        "pagination": {
            "type": "page_number",
            "limit_param": "page_size",
            "offset_param": "page_number",
            "limit": 500
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "%Y%m%d"
        }
    },
    {
        "name": "documents",
        "path": "/projects/{project_id}/register",
        "method": "GET",
        "data_selector": "RegisterSearch.Document",
        "primary_key": "DocumentId",
        "pagination": {
            "type": "page_number",
            "limit_param": "page_size",
            "offset_param": "page_number",
            "limit": 500
        },
        "incremental": {
            "cursor_path": "registered",
            "param_name": "search_query",
            "format": "%Y%m%d"
        }
    },
    {
        "name": "mail",
        "path": "/projects/{project_id}/mail",
        "method": "GET",
        "data_selector": "MailSearch.Mail",
        "primary_key": "MailId",
        "pagination": {
            "type": "page_number",
            "limit_param": "page_size",
            "offset_param": "page_number",
            "limit": 500
        },
        "incremental": {
            "cursor_path": "sentdate",
            "param_name": "search_query",
            "format": "%Y%m%d"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "documents",
        "depends_on": "projects",
        "param_mapping": {"project_id": "ProjectId"}
    },
    {
        "endpoint": "mail",
        "depends_on": "projects",
        "param_mapping": {"project_id": "ProjectId"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate with the Aconex REST API using a dlt data pipeline. Adjust the configuration as needed based on specific project requirements or API updates.

---
*dlt REST API Source Configuration Guide*