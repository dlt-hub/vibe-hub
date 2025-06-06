# Peloton REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Peloton API

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Peloton API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.onepeloton.com/`
- This is the base URL for accessing the Peloton API.

**Authentication:**
- **Type:** `custom`
- **Details:** The Peloton API requires a custom authentication mechanism using a username and password. The `pylotoncycle` library is used to handle authentication.
- **Parameters:**
  - `username`: Your Peloton Username.
  - `password`: Your Peloton Password.

## 2. Available Endpoints

### Workouts Endpoint

**Endpoint Details:**
- **Path:** `/api/user/{user_id}/workouts`
- **HTTP Method:** `GET`
- **Required Query Parameters:**
  - `limit`: Number of records to fetch per request.
  - `page`: Page number for pagination.
- **Response Data Structure:** JSON object containing workout details.

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:**
  - `limit`: Controls the number of records per page.
  - `page`: Specifies the page number.
- **Default/Maximum Page Sizes:** Typically set to 100 records per page.

**Data Extraction:**
- **JSONPath:** `$.data`
- **Primary Key:** `id`
- **Key Fields:** `id`, `created_at`, `user_id`

### Workout Metrics Endpoint

**Endpoint Details:**
- **Path:** `/api/workout/{workout_id}/metrics`
- **HTTP Method:** `GET`
- **Required Path Parameters:**
  - `workout_id`: The ID of the workout for which metrics are being fetched.
- **Response Data Structure:** JSON object containing workout metrics.

**Data Extraction:**
- **Primary Key:** `id`
- **Key Fields:** `duration`, `average_summaries`, `effort_zones`

## 3. Incremental Data Loading

**Incremental Support:**
- **Parameter:** `created_at`
- **Date/Time Field:** `created_at`
- **Format:** Unix timestamps
- **Recommended Initial Values:** Use the current timestamp minus a reasonable buffer period (e.g., 30 days) for the first sync.

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Workouts and Metrics:**
  - The `WorkoutMetricsStream` depends on the `WorkoutsStream`.
  - **Mapping:** `workout_id` from `WorkoutsStream` to `WorkoutMetricsStream`.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests:** The Peloton API does not publicly document rate limits, but it is recommended to implement a delay between requests to avoid throttling.

**Special Requirements:**
- **Custom Headers:** None required beyond authentication.
- **Response Format:** JSON
- **Error Handling:** Implement retry logic for transient errors and handle HTTP status codes appropriately.
- **Data Type Specifications:** Ensure correct data types are used as per the schema definitions.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.onepeloton.com/",
    "auth": {
        "type": "custom",
        "username": "{username}",
        "password": "{password}"
    }
}

ENDPOINTS = [
    {
        "name": "workouts",
        "path": "/api/user/{user_id}/workouts",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "limit",
            "page_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "created_at",
            "param_name": "created_at",
            "format": "unix"
        }
    },
    {
        "name": "workout_metrics",
        "path": "/api/workout/{workout_id}/metrics",
        "method": "GET",
        "data_selector": "metrics",
        "primary_key": "id"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "workout_metrics",
        "depends_on": "workouts",
        "param_mapping": {"workout_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate with the Peloton API using a dlt data pipeline. Adjust the parameters as needed based on specific requirements and API updates.

---
*dlt REST API Source Configuration Guide*