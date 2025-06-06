# Aws Cost Explorer REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This documentation provides a comprehensive guide to configuring a dlt data pipeline for the AWS Cost Explorer REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- Base URL: AWS Cost Explorer does not use a traditional REST API URL structure. Instead, it uses the AWS SDK to interact with the service.

**Authentication:**
- Type: `aws_signature`
- Required Parameters:
  - `access_key`: Your AWS Account Access Key.
  - `secret_key`: Your AWS Account Secret Key.
  - `session_token`: (Optional) Your AWS Account Session Token if required for authentication.

## 2. Available Endpoints

### Cost and Usage with Resources

**Endpoint Details:**
- Path: AWS SDK method `get_cost_and_usage`
- HTTP Method: N/A (uses AWS SDK)
- Required Parameters:
  - `TimePeriod`: A dictionary with `Start` and `End` dates in "YYYY-MM-DD" format.
  - `Granularity`: "MONTHLY", "DAILY", or "HOURLY".
  - `Metrics`: List of metrics such as "AmortizedCost", "BlendedCost", etc.

**Pagination Configuration:**
- Type: `token_based`
- Parameter: `NextPageToken`
- How next page is determined: Use the `NextPageToken` from the response to fetch subsequent pages.

**Data Extraction:**
- JSONPath: `ResultsByTime`
- Key Fields: `time_period_start`, `metric_name`

## 3. Incremental Data Loading

**Incremental Support:**
- Parameter: `TimePeriod`
- Date Field: `time_period_start` in the response
- Format: ISO 8601 dates ("YYYY-MM-DD")
- Recommended Initial Value: Use the `start_date` from the configuration.

## 4. Endpoint Dependencies

**Resource Relationships:**
- No direct dependencies between endpoints as AWS Cost Explorer primarily provides aggregated data.

## 5. API Behavior & Limits

**Rate Limiting:**
- AWS imposes limits on the number of requests per second. Refer to AWS documentation for specific limits.

**Special Requirements:**
- Required Headers: AWS Signature Version 4 headers for authentication.
- Response Format: JSON
- Error Handling: Handle AWS-specific errors such as `ThrottlingException`.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "AWS SDK",
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
        "method": "get_cost_and_usage",
        "data_selector": "ResultsByTime",
        "primary_key": ["metric_name", "time_period_start"],
        "pagination": {
            "type": "token_based",
            "token_param": "NextPageToken"
        },
        "incremental": {
            "cursor_path": "time_period_start",
            "param_name": "TimePeriod",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for extracting data from the AWS Cost Explorer using the AWS SDK. Adjust the configuration as needed based on specific use cases and AWS account settings.

---
*dlt REST API Source Configuration Guide*