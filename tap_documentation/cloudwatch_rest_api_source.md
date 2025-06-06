# Cloudwatch REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the AWS CloudWatch Logs Insights API using the `tap-cloudwatch` connector. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- The AWS CloudWatch Logs Insights API does not have a fixed base URL. Instead, it uses AWS SDKs to interact with the service. The endpoint URL can be specified if needed, otherwise, the default AWS endpoints are used.

**Authentication:**
- Type: `aws_credentials`
- Required Parameters:
  - `aws_access_key_id`: The access key for your AWS account.
  - `aws_secret_access_key`: The secret key for your AWS account.
  - `aws_session_token`: The session token for temporary credentials (optional).
  - `aws_profile`: The AWS credentials profile name (optional).
  - `aws_region_name`: The AWS region name (e.g., `us-east-1`).

## 2. Available Endpoints

### Endpoint: Log Stream

**Endpoint Details:**
- Path: N/A (AWS SDK handles endpoint resolution)
- HTTP Method: N/A (AWS SDK handles HTTP methods)
- Required Parameters:
  - `log_group_name`: The log group on which to perform the query.
  - `query`: The query string to use, following the [CloudWatch Logs Insights Query Syntax](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html).

**Pagination Configuration:**
- Type: `batch_window`
- Parameters:
  - `batch_increment_s`: The size of the time window to query by, default is 3600 seconds (1 hour).
- Pagination is handled by splitting the query into time windows and iterating over them.

**Data Extraction:**
- JSONPath: The data is extracted from the `results` field in the API response.
- Primary Key: `ptr` (The identifier for the log record)
- Key Fields: `timestamp` (The timestamp of the log)

## 3. Incremental Data Loading

**Incremental Support:**
- Incremental fetching is supported using the `timestamp` field.
- The `start_date` parameter specifies the earliest record date to sync.
- The `end_date` parameter specifies the last record date to sync, with a 5-minute buffer to allow logs to arrive in full.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `log` stream does not have explicit dependencies on other endpoints, but it requires valid AWS credentials and configuration to access the CloudWatch Logs Insights API.

## 5. API Behavior & Limits

**Rate Limiting:**
- The AWS CloudWatch Logs Insights API has a limit of 20 concurrent queries.
- Each query can return a maximum of 10,000 records. If this limit is exceeded, the query is split into smaller sub-batches.

**Special Requirements:**
- Custom Headers: N/A (handled by AWS SDK)
- Response Format: The response includes a `results` array with log records.
- Error Handling: The API handles retries for failed queries and logs status messages.
- Data Type Specifications: The `timestamp` field is used as the replication key and must be included in the query.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "auth": {
        "type": "aws_credentials",
        "aws_access_key_id": "<your_access_key_id>",
        "aws_secret_access_key": "<your_secret_access_key>",
        "aws_session_token": "<your_session_token>",
        "aws_profile": "<your_aws_profile>",
        "aws_region_name": "us-east-1"
    }
}

ENDPOINTS = [
    {
        "name": "log",
        "data_selector": "results",
        "primary_key": "ptr",
        "pagination": {
            "type": "batch_window",
            "batch_increment_s": 3600
        },
        "incremental": {
            "cursor_path": "timestamp",
            "param_name": "start_date",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and setup to effectively extract data from the AWS CloudWatch Logs Insights API using the `tap-cloudwatch` connector in a dlt data pipeline.

---
*dlt REST API Source Configuration Guide*