# Jira REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Jira

This document provides a comprehensive guide to configuring a dlt REST API source for Jira using the `tap-jira` connector. The configuration is structured to facilitate seamless data extraction from Jira's REST API.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://{domain}:443/rest/api/3`
  - Replace `{domain}` with your Jira account domain, e.g., `meltano.atlassian.net`.

### Authentication
- **Type**: `basic`
- **Parameters**:
  - **Username**: Jira account email
  - **Password**: Jira API token
- **Headers**:
  - `User-Agent`: Optional custom user agent string

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/users/search`
- **Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array of user objects

### Issues Endpoint
- **Path**: `/search`
- **Method**: `GET`
- **Query Parameters**:
  - `jql`: JQL query string for filtering issues
  - `startAt`: Pagination offset
  - `maxResults`: Number of results per page
- **Response Data Structure**: JSON object with `issues` array

### Projects Endpoint
- **Path**: `/project/search`
- **Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON object with `values` array

### Pagination Configuration
- **Type**: `offset`
- **Parameter Names**:
  - `startAt`: Offset for pagination
  - `maxResults`: Limit for pagination
- **Default/Maximum Page Sizes**: Default is 100 for issues

### Data Extraction
- **JSONPath**:
  - Users: `$[*]`
  - Issues: `$[issues][*]`
  - Projects: `$[values][*]`
- **Primary Key**:
  - Users: `accountId`
  - Issues: `id`
  - Projects: `id`

## 3. Incremental Data Loading

### Incremental Support
- **Issues Endpoint**:
  - **Query Parameter**: `jql`
  - **Date/Timestamp Field**: `created`, `updated`
  - **Format**: ISO 8601
  - **Recommended Initial Values**: Use `start_date` from configuration

## 4. Endpoint Dependencies

### Resource Relationships
- **Issues** depend on **Projects** for project-specific data.
- **Issue Comments**, **Worklogs**, and **Watchers** are child streams of **Issues**.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Hour**: Subject to Jira's API rate limits, typically 1000 requests per hour.
- **Burst Limits**: Handle with exponential backoff on 429 responses.

### Special Requirements
- **Custom Headers**: None required beyond authentication
- **Response Format Considerations**: JSON
- **Error Handling Patterns**: Retry on 429, handle 400 for unsupported operations

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{domain}:443/rest/api/3",
    "auth": {
        "type": "basic",
        "username": "{email}",
        "password": "{api_token}"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users/search",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "accountId",
        "pagination": {
            "type": "offset",
            "limit_param": "maxResults",
            "offset_param": "startAt",
            "limit": 100
        }
    },
    {
        "name": "issues",
        "path": "/search",
        "method": "GET",
        "data_selector": "$[issues][*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "maxResults",
            "offset_param": "startAt",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated",
            "param_name": "jql",
            "format": "iso"
        }
    },
    {
        "name": "projects",
        "path": "/project/search",
        "method": "GET",
        "data_selector": "$[values][*]",
        "primary_key": "id"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "issue_comments",
        "depends_on": "issues",
        "param_mapping": {"issue_id": "id"}
    },
    {
        "endpoint": "issue_worklogs",
        "depends_on": "issues",
        "param_mapping": {"issue_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for Jira's REST API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*