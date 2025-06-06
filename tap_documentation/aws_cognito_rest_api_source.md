# Aws Cognito REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for AWS Cognito

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the AWS Cognito REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- The AWS Cognito API does not have a traditional base URL like other REST APIs. Instead, it uses AWS SDKs to interact with services. However, for the purpose of this configuration, consider the service endpoint as the base URL.
- Example: `"https://cognito-idp.{region}.amazonaws.com/"`

**Authentication:**
- Type: `aws_signature`
- AWS uses signature-based authentication. Ensure that your AWS credentials (Access Key ID and Secret Access Key) are configured in your environment or provided through the AWS SDK.
- No additional parameters are required in the request headers for authentication as the AWS SDK handles this.

## 2. Available Endpoints

### Users Endpoint

**Endpoint Details:**
- Path: `/list_users`
- HTTP Method: `GET`
- Required Query Parameters:
  - `UserPoolId`: The ID of the user pool from which to list users.
- Optional Query Parameters:
  - `PaginationConfig.PageSize`: The maximum number of users to return.
- Response Data Structure: The response contains a `Users` array with user details.

**Pagination Configuration:**
- Type: `page_number`
- Parameter Names:
  - `PaginationConfig.PageSize`: Controls the number of records per page.
- Default/Maximum Page Sizes: The maximum page size is 60.
- Pagination is handled using AWS Paginator, which automatically manages the retrieval of subsequent pages.

**Data Extraction:**
- JSONPath: `Users`
- Key Fields: `Username` uniquely identifies each user record.

## 3. Incremental Data Loading

**Incremental Support:**
- The AWS Cognito API does not natively support incremental data loading via query parameters. However, you can implement custom logic to filter users based on attributes like `updated_at` if available in your data model.
- Date/Timestamp Field: Not directly available; consider using user attributes if applicable.
- Expected Format: ISO 8601 dates or Unix timestamps if implemented.
- Recommended Initial Values: Start with the earliest possible date or timestamp if implementing custom logic.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `Users` endpoint does not have direct dependencies on other endpoints within the AWS Cognito API.
- If integrating with other AWS services, ensure that identifiers such as `UserPoolId` are correctly mapped.

## 5. API Behavior & Limits

**Rate Limiting:**
- AWS APIs have rate limits that vary by service and region. Monitor your usage and refer to AWS documentation for specific limits.
- Implement retry logic with exponential backoff to handle throttling.

**Special Requirements:**
- Required Custom Headers: None, as AWS SDK handles necessary headers.
- Response Format Considerations: Ensure JSON parsing is correctly implemented.
- Error Handling Patterns: Implement error handling for common AWS errors such as `ThrottlingException` and `LimitExceededException`.
- Data Type Specifications: Ensure that data types in your schema match those returned by the API.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://cognito-idp.{region}.amazonaws.com/",
    "auth": {
        "type": "aws_signature"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/list_users",
        "method": "GET",
        "data_selector": "Users",
        "primary_key": "Username",
        "pagination": {
            "type": "page_number",
            "limit_param": "PaginationConfig.PageSize",
            "limit": 60
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and considerations for setting up a dlt data pipeline to extract data from the AWS Cognito API. Adjust the configuration as needed based on your specific use case and data requirements.

---
*dlt REST API Source Configuration Guide*