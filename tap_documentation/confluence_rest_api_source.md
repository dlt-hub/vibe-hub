# Confluence REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Confluence API

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Confluence REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://your-domain.atlassian.net/wiki/rest/api/`
- Ensure to replace `your-domain` with your actual Confluence domain.

**Authentication:**
- **Type:** `basic`
- **Details:**
  - **Email:** Your Confluence account email.
  - **API Token:** Generated from your Atlassian account.
  - **Header:** `Authorization: Basic {base64_encoded_email_and_token}`

## 2. Available Endpoints

### Endpoint: Groups
- **Path:** `/group`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON array of group objects.
- **Primary Key:** `id`

### Endpoint: Spaces
- **Path:** `/space`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON array of space objects.
- **Primary Key:** `id`
- **Expand Options:** `permissions`, `icon`, `description.plain`, `description.view`

### Endpoint: Themes
- **Path:** `/settings/theme`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON array of theme objects.
- **Primary Key:** `themeKey`
- **Expand Options:** `icon`

### Endpoint: Pages
- **Path:** `/content`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON array of page objects.
- **Primary Key:** `id`
- **Content Type:** `page`
- **Expand Options:** `history`, `history.lastUpdated`, `history.previousVersion`, `history.contributors`, `restrictions.read.restrictions.user`, `version`, `descendants.comment`

### Endpoint: Blogposts
- **Path:** `/content`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON array of blogpost objects.
- **Primary Key:** `id`
- **Content Type:** `blogpost`
- **Expand Options:** Same as Pages

## Pagination Configuration

- **Type:** `offset`
- **Parameters:**
  - **Limit Parameter:** `limit`
  - **Offset Parameter:** `start`
  - **Default Limit:** 100
- **Next Page Determination:** Increment `start` by `limit` until the `size` of the response is less than `limit`.

## 3. Incremental Data Loading

**Incremental Support:**
- **Parameter:** `updated_at`
- **Format:** ISO 8601 date format
- **Recommended Initial Value:** The earliest date from which you want to start syncing data.

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Pages and Blogposts:** Both depend on the `/content` endpoint but differ by `content_type` parameter (`page` or `blogpost`).
- **Mapping Identifiers:** Use `id` fields to map between related resources.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Hour:** Typically 1000 requests per hour for Confluence Cloud.
- **Burst Limits:** Avoid making more than 100 requests per minute.

**Special Requirements:**
- **Custom Headers:** `User-Agent` header is recommended for identifying your application.
- **Response Format:** JSON
- **Error Handling:** Handle HTTP status codes like 429 (Too Many Requests) by implementing retry logic with exponential backoff.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://your-domain.atlassian.net/wiki/rest/api/",
    "auth": {
        "type": "basic",
        "email": "your-email@example.com",
        "api_token": "your-api-token"
    }
}

ENDPOINTS = [
    {
        "name": "groups",
        "path": "/group",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "start",
            "limit": 100
        }
    },
    {
        "name": "spaces",
        "path": "/space",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "start",
            "limit": 100
        }
    },
    {
        "name": "themes",
        "path": "/settings/theme",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "themeKey",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "start",
            "limit": 100
        }
    },
    {
        "name": "pages",
        "path": "/content",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "start",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "history.lastUpdated.when",
            "param_name": "updated_at",
            "format": "iso"
        }
    },
    {
        "name": "blogposts",
        "path": "/content",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "start",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "history.lastUpdated.when",
            "param_name": "updated_at",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "pages",
        "depends_on": "content",
        "param_mapping": {"type": "page"}
    },
    {
        "endpoint": "blogposts",
        "depends_on": "content",
        "param_mapping": {"type": "blogpost"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the Confluence REST API using a dlt data pipeline. Adjust the configuration as needed based on your specific requirements and API usage limits.

---
*dlt REST API Source Configuration Guide*