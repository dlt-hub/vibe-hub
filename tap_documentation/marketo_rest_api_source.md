# Marketo REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Marketo

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Marketo REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://123-ABC-456.mktorest.com/rest/`
- This URL includes the domain specific to your Marketo instance.

**Authentication:**
- Type: `oauth2`
- Token URL: `https://123-ABC-456.mktorest.com/identity/oauth/token`
- Required Scopes: Not explicitly defined in the code, but typically includes access to read data.
- Parameters:
  - `client_id`: Your Marketo client ID.
  - `client_secret`: Your Marketo client secret.

## 2. Available Endpoints

### Leads Endpoint
**Endpoint Details:**
- Path: `/v1/leads/describe.json`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON with lead details.

**Pagination Configuration:**
- Type: `offset`
- Limit Parameter: `maxReturn`
- Offset Parameter: `offset`
- Default/Maximum Page Sizes: 200

**Data Extraction:**
- JSONPath: `result`
- Primary Key: `id`

### Activities Endpoint
**Endpoint Details:**
- Path: `/v1/activities/types.json`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON with activity types.

**Pagination Configuration:**
- Type: `single_page`
- No pagination parameters required.

**Data Extraction:**
- JSONPath: `result`
- Primary Key: `marketoGUID`

## 3. Incremental Data Loading

**Incremental Support:**
- Leads: Uses `updatedAt` for incremental loading.
- Activities: Uses `activityDate` for incremental loading.

**Date/Time Field:**
- Leads: `updatedAt`
- Activities: `activityDate`

**Expected Format:**
- ISO 8601 date format (e.g., `2023-10-01T00:00:00Z`)

**Recommended Initial Values:**
- Use the `start_date` from the configuration for initial sync.

## 4. Endpoint Dependencies

**Resource Relationships:**
- Activities depend on activity types for metadata.
- Leads may require campaign data for full context.

**Mapping Identifiers:**
- Use `leadId` to map between leads and activities.

**Processing Order:**
- Fetch activity types before activities to ensure metadata is available.

## 5. API Behavior & Limits

**Rate Limiting:**
- Daily Limit: 50,000 requests
- Short-term Limit: 100 requests per 20 seconds
- Recommended Delay: Implement backoff strategy for rate limit errors.

**Special Requirements:**
- Custom Headers: `Authorization: Bearer {token}`
- Response Format: JSON
- Error Handling: Implement retries for transient errors and handle specific error codes like `1035` for Corona support.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://123-ABC-456.mktorest.com/rest/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://123-ABC-456.mktorest.com/identity/oauth/token",
        "client_id": "your_client_id",
        "client_secret": "your_client_secret"
    }
}

ENDPOINTS = [
    {
        "name": "leads",
        "path": "/v1/leads/describe.json",
        "method": "GET",
        "data_selector": "result",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "maxReturn",
            "offset_param": "offset",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "updatedAt",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "activities",
        "path": "/v1/activities/types.json",
        "method": "GET",
        "data_selector": "result",
        "primary_key": "marketoGUID",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "activityDate",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "activities",
        "depends_on": "activity_types",
        "param_mapping": {"activityTypeId": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline with the Marketo REST API, ensuring efficient data extraction and handling of API constraints.

---
*dlt REST API Source Configuration Guide*