# Aws Cost Explorer REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the AWS Cost Explorer API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://ce.us-east-1.amazonaws.com/`
- Note: AWS Cost Explorer API does not have a versioned path.

**Authentication:**
- Type: `aws_signature`
- Required Parameters:
  - `access_key`: Your AWS Account Access Key.
  - `secret_key`: Your AWS Account Secret Key.
  - `session_token`: Optional, required if using temporary credentials.

## 2. Available Endpoints

### Cost and Usage with Resources

**Endpoint Details:**
- Path: `/`
- HTTP Method: `POST`
- Required Parameters:
  - `TimePeriod`: Start and End dates in `YYYY-MM-DD` format.
  - `Granularity`: `DAILY`, `MONTHLY`, or `HOURLY`.
  - `Metrics`: List of metrics such as `AmortizedCost`, `BlendedCost`, etc.

**Pagination Configuration:**
- Type: `cursor`
- Parameter: `NextPageToken`
- How next page is determined: Use the `NextPageToken` from the response to fetch subsequent pages.

**Data Extraction:**
- JSONPath: `ResultsByTime`
- Key Fields: `time_period_start`, `metric_name`

## 3. Incremental Data Loading

**Incremental Support:**
- Query Parameter: `TimePeriod`
- Date Field: `time_period_start`
- Format: `YYYY-MM-DD`
- Recommended Initial Value: Start date as per business requirement, e.g., `2023-01-01`.

## 4. Endpoint Dependencies

**Resource Relationships:**
- No direct dependencies between endpoints as the AWS Cost Explorer API is designed to provide aggregated data.

## 5. API Behavior & Limits

**Rate Limiting:**
- AWS imposes limits on API requests. Check AWS documentation for specific limits and consider implementing exponential backoff for retries.

**Special Requirements:**
- Required Headers: AWS Signature Version 4 headers for authentication.
- Response Format: JSON
- Error Handling: Implement retries for `ThrottlingException` and other transient errors.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://ce.us-east-1.amazonaws.com/",
    "auth": {
        "type": "aws_signature",
        "access_key": "YOUR_ACCESS_KEY",
        "secret_key": "YOUR_SECRET_KEY",
        "session_token": "YOUR_SESSION_TOKEN"
    }
}

ENDPOINTS = [
    {
        "name": "cost_and_usage",
        "path": "/",
        "method": "POST",
        "data_selector": "ResultsByTime",
        "primary_key": ["metric_name", "time_period_start"],
        "pagination": {
            "type": "cursor",
            "cursor_param": "NextPageToken"
        },
        "incremental": {
            "cursor_path": "time_period_start",
            "param_name": "TimePeriod",
            "format": "YYYY-MM-DD"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the AWS Cost Explorer API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*