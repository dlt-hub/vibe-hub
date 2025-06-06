# Launchdarkly REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the LaunchDarkly REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://app.launchdarkly.com/api/v2/`

**Authentication:**
- Type: `api_key`
- Location: `header`
- Parameter Name: `Authorization`
- Format: `Bearer {auth_token}`

## 2. Available Endpoints

### Projects Endpoint

**Endpoint Details:**
- Path: `/projects`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON array located at `$.items[*]`

**Pagination Configuration:**
- Type: `single_page` (No pagination required)

**Data Extraction:**
- JSONPath: `$.items[*]`
- Primary Key: `_id`

### Feature Flags Endpoint

**Endpoint Details:**
- Path: `/flags/{project_key}`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON array located at `$.items[*]`

**Pagination Configuration:**
- Type: `single_page` (No pagination required)

**Data Extraction:**
- JSONPath: `$.items[*]`
- Primary Key: `project_key`, `key`

### Feature Flag Targets Endpoint

**Endpoint Details:**
- Path: `/flags/{project_key}/{feature_flag_key}`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON arrays located at `$.environments.{environment}.targets[*]` and `$.environments.{environment}.contextTargets[*]`

**Pagination Configuration:**
- Type: `single_page` (No pagination required)

**Data Extraction:**
- JSONPath: `$.environments.{environment}.targets[*]` and `$.environments.{environment}.contextTargets[*]`
- Primary Key: `project_key`, `feature_flag_key`, `target`

## 3. Incremental Data Loading

**Incremental Support:**
- Not applicable for the current endpoints as they do not support incremental data fetching.

## 4. Endpoint Dependencies

**Resource Relationships:**
- `FeatureFlags` depends on `Projects` for the `project_key`.
- `FeatureFlagTargets` depends on `FeatureFlags` for both `project_key` and `feature_flag_key`.

**Processing Order:**
1. Fetch data from `Projects`.
2. Use `project_key` from `Projects` to fetch `FeatureFlags`.
3. Use `project_key` and `feature_flag_key` from `FeatureFlags` to fetch `FeatureFlagTargets`.

## 5. API Behavior & Limits

**Rate Limiting:**
- Specific rate limits are not detailed in the provided code. Refer to LaunchDarkly API documentation for rate limits.

**Special Requirements:**
- Custom Headers: `User-Agent` can be set via configuration.
- Response Format: JSON
- Error Handling: Implement error handling as per standard REST API practices.
- Data Type Specifications: Ensure data types match the schema definitions provided in the stream classes.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://app.launchdarkly.com/api/v2/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {auth_token}"
    }
}

ENDPOINTS = [
    {
        "name": "projects",
        "path": "/projects",
        "method": "GET",
        "data_selector": "$.items[*]",
        "primary_key": "_id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "feature_flags",
        "path": "/flags/{project_key}",
        "method": "GET",
        "data_selector": "$.items[*]",
        "primary_key": ["project_key", "key"],
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "feature_flag_targets",
        "path": "/flags/{project_key}/{feature_flag_key}",
        "method": "GET",
        "data_selector": "$.environments.{environment}.targets[*]",
        "primary_key": ["project_key", "feature_flag_key", "target"],
        "pagination": {
            "type": "single_page"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "feature_flags",
        "depends_on": "projects",
        "param_mapping": {"project_key": "key"}
    },
    {
        "endpoint": "feature_flag_targets",
        "depends_on": "feature_flags",
        "param_mapping": {"project_key": "project_key", "feature_flag_key": "key"}
    }
]
```

This configuration guide provides all necessary parameters and instructions to set up a dlt data pipeline for the LaunchDarkly API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*