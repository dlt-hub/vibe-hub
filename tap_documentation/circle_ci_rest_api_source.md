# Circle Ci REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the CircleCI REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://circleci.com/api/v2/`
- **Version Path**: `/v2/`
- **Common Prefix**: `/project/{project_slug}/`

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Circle-Token`
- **Format**: `Bearer {token}`

## 2. Available Endpoints

### Pipelines Endpoint
- **Path**: `/project/{project_slug}/pipeline`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `branch`, `page-token`
- **Response Data Structure**: JSON with a key `items` containing pipeline data

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Name**: `page-token`
- **Next Page Determination**: Use `next_page_token` from the response to fetch subsequent pages
- **Default/Maximum Page Sizes**: Not explicitly defined, depends on API defaults

### Data Extraction
- **JSONPath to Data Array**: `$.items`
- **Record Identification**: Each pipeline record is identified by the `id` field
- **Primary Key**: `id`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value**: Current date minus a buffer (e.g., 7 days)

## 4. Endpoint Dependencies

### Resource Relationships
- **Workflows Endpoint**: Depends on `pipelines` endpoint
  - **Path**: `/pipeline/{pipeline_id}/workflow`
  - **Mapping**: Use `pipeline_id` from pipelines to fetch workflows
- **Jobs Endpoint**: Depends on `workflows` endpoint
  - **Path**: `/workflow/{workflow_id}/job`
  - **Mapping**: Use `workflow_id` from workflows to fetch jobs

### Required Processing Order
1. Pipelines
2. Workflows (dependent on Pipelines)
3. Jobs (dependent on Workflows)

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: Not explicitly defined, adhere to best practices by implementing retry logic with exponential backoff

### Special Requirements
- **Custom Headers**: `Circle-Token` for authentication
- **Response Format Considerations**: JSON responses with potential nested structures
- **Error Handling Patterns**: Handle HTTP 401, 403, and 404 errors with appropriate retry or fail logic
- **Data Type Specifications**: Ensure date fields are parsed as ISO 8601 strings

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://circleci.com/api/v2/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Circle-Token",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "pipelines",
        "path": "/project/{project_slug}/pipeline",
        "method": "GET",
        "data_selector": "items",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "page-token"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "workflows",
        "path": "/pipeline/{pipeline_id}/workflow",
        "method": "GET",
        "data_selector": "items",
        "primary_key": "id"
    },
    {
        "name": "jobs",
        "path": "/workflow/{workflow_id}/job",
        "method": "GET",
        "data_selector": "items",
        "primary_key": "id"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "workflows",
        "depends_on": "pipelines",
        "param_mapping": {"pipeline_id": "id"}
    },
    {
        "endpoint": "jobs",
        "depends_on": "workflows",
        "param_mapping": {"workflow_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline to extract data from the CircleCI REST API effectively.

---
*dlt REST API Source Configuration Guide*