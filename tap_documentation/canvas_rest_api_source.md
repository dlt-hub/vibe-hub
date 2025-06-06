# Canvas REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Canvas REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://dtechhs.instructure.com/api/v1`
- This URL serves as the root for all API requests.

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {token}`
- **Parameter Name**: `Authorization`
- **Location**: Header
- The API requires a bearer token for authentication, which should be included in the request headers.

## 2. Available Endpoints

### Enrollment Terms
- **Path**: `/accounts/1/terms`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON array located at `$.enrollment_terms[*]`
- **Primary Key**: `id`

### Courses
- **Path**: `/accounts/1/courses`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON array located at `$[*]`
- **Primary Key**: `id`

### Outcome Results
- **Path**: `/courses/{course_id}/outcome_results`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON object with key `outcome_results`
- **Primary Key**: `id`

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**: `page`, `per_page`
- **Default Page Size**: 100
- **Next Page Determination**: Based on `next` link in response headers

## 3. Incremental Data Loading

### Incremental Support
- **Courses Endpoint**: Supports incremental loading using `ends_after` query parameter.
- **Date Field**: `updated_at`
- **Date Format**: ISO 8601
- **Initial Value**: Use the current date minus a reasonable buffer period for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Outcome Results**: Depends on `courses` endpoint for `course_id`.
- **Enrollments**: Depends on `courses` endpoint for `course_id`.
- **Sections**: Depends on `courses` endpoint for `course_id`.

### Mapping Identifiers
- **Course ID**: Used to map between `courses`, `outcome_results`, `enrollments`, and `sections`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Minute**: Subject to Canvas API limits (typically 700 requests per hour).
- **Burst Limits**: Implement retry logic with exponential backoff for handling rate limits.

### Special Requirements
- **Custom Headers**: Include `User-Agent` if specified in the configuration.
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for 5xx errors and handle 4xx errors gracefully.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://dtechhs.instructure.com/api/v1",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "enrollment_terms",
        "path": "/accounts/1/terms",
        "method": "GET",
        "data_selector": "$.enrollment_terms[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 100
        }
    },
    {
        "name": "courses",
        "path": "/accounts/1/courses",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "ends_after",
            "format": "iso"
        }
    },
    {
        "name": "outcome_results",
        "path": "/courses/{course_id}/outcome_results",
        "method": "GET",
        "data_selector": "outcome_results",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 100
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "outcome_results",
        "depends_on": "courses",
        "param_mapping": {"course_id": "id"}
    },
    {
        "endpoint": "enrollments",
        "depends_on": "courses",
        "param_mapping": {"course_id": "id"}
    },
    {
        "endpoint": "sections",
        "depends_on": "courses",
        "param_mapping": {"course_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline to extract data from the Canvas REST API effectively.

---
*dlt REST API Source Configuration Guide*