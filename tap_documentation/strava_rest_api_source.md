# Strava REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt - Strava API

This document provides a comprehensive guide to configuring a dlt data pipeline for the Strava API using the provided Python code. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://www.strava.com/api/v3/`

**Authentication:**
- Type: `oauth2`
- Token URL: `https://www.strava.com/oauth/token`
- Required Scopes: Not explicitly defined in the code, but typically includes `read`, `activity:read`, etc.
- Parameters:
  - `client_id`: Your Strava application client ID
  - `client_secret`: Your Strava application client secret
  - `refresh_token`: Scoped refresh token obtained from the Strava OAuth flow

## 2. Available Endpoints

### Activities
- **Path:** `/athlete/activities`
- **Method:** GET
- **Query Parameters:**
  - `access_token`: OAuth access token
  - `per_page`: Number of results per page (default 30)
  - `page`: Page number for pagination
  - `after`: Start date for filtering activities (epoch time)
  - `before`: End date for filtering activities (epoch time)
- **Response Data Structure:** JSON array of activity objects
- **Data Extraction:**
  - JSONPath: Root array
  - Primary Key: `id`

### Activity Kudoers
- **Path:** `/activities/{activity_id}/kudos`
- **Method:** GET
- **Query Parameters:**
  - `access_token`: OAuth access token
- **Response Data Structure:** JSON array of kudoer objects
- **Data Extraction:**
  - JSONPath: Root array

### Activity Comments
- **Path:** `/activities/{activity_id}/comments`
- **Method:** GET
- **Query Parameters:**
  - `access_token`: OAuth access token
  - `per_page`: Number of results per page (default 30)
  - `after_cursor`: Cursor for pagination
- **Response Data Structure:** JSON array of comment objects
- **Data Extraction:**
  - JSONPath: Root array
  - Primary Key: `id`
  - Incremental Key: `created_at`

## 3. Incremental Data Loading

**Incremental Support:**
- **Activities Stream:**
  - Query Parameter: `after`
  - Date Field: `start_date`
  - Format: Epoch time
  - Recommended Initial Value: Earliest date of interest in epoch time

- **Activity Comments Stream:**
  - Query Parameter: `after_cursor`
  - Date Field: `created_at`
  - Format: ISO 8601

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Activity Kudoers and Comments** depend on the `Activities` endpoint.
- **Mapping:** `activity_id` from `Activities` is used in the path for both `Activity Kudoers` and `Activity Comments`.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Per 15 Minutes:** Defined by `x-ratelimit-limit` header
- **Daily Limit:** Defined by `x-ratelimit-limit` header
- **Handling:** Sleep for 15 minutes if the 15-minute limit is exceeded; exit if the daily limit is exceeded.

**Special Requirements:**
- **Custom Headers:** None specified beyond standard OAuth headers
- **Response Format:** JSON
- **Error Handling:** Custom validation to handle rate limits and retry logic for server errors

## Output Format

```python
# REST API Configuration for dlt - Strava API

BASE_CONFIG = {
    "base_url": "https://www.strava.com/api/v3/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://www.strava.com/oauth/token",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "refresh_token": "<your_refresh_token>"
    }
}

ENDPOINTS = [
    {
        "name": "activities",
        "path": "/athlete/activities",
        "method": "GET",
        "data_selector": "root",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "page_param": "page",
            "limit": 30
        },
        "incremental": {
            "cursor_path": "start_date",
            "param_name": "after",
            "format": "epoch"
        }
    },
    {
        "name": "activity_kudoers",
        "path": "/activities/{activity_id}/kudos",
        "method": "GET",
        "data_selector": "root"
    },
    {
        "name": "activity_comments",
        "path": "/activities/{activity_id}/comments",
        "method": "GET",
        "data_selector": "root",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "after_cursor"
        },
        "incremental": {
            "cursor_path": "created_at",
            "param_name": "after_cursor",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "activity_kudoers",
        "depends_on": "activities",
        "param_mapping": {"activity_id": "id"}
    },
    {
        "endpoint": "activity_comments",
        "depends_on": "activities",
        "param_mapping": {"activity_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate with the Strava API using a dlt data pipeline. Adjust the configuration as needed based on your specific use case and data requirements.

---
*dlt REST API Source Configuration Guide*