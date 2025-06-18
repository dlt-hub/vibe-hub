# Overview

This document provides a configuration example for integrating with a third-party SMS API using dltHub's REST API source format. The integration allows for the retrieval of SMS data, including details such as cost, creation timestamp, sender and recipient information, and status. The configuration supports offset-based pagination and requires authentication via a bearer token.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v4",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "ID",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "sms_data",
            "endpoint": {
                "path": "/sms",
                "method": "GET",
                "params": {
                    "fromTS": dlt.config["start_date"],
                    "ToTS": dlt.config["end_date"]
                },
                "data_selector": "Data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "Offset"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_token"]`

# Resources

- **Resource Name**: sms_data
- **Endpoint Path**: `/sms`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset-based
  - **Limit**: 100
  - **Offset Parameter**: Offset
- **Data Selector**: Data

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Factor**: 2
- **Max Retries**: 3

# Schema

The schema for the SMS data includes the following fields:

- **Cost**: Object containing:
  - **Currency**: String, the currency in which the cost is calculated.
  - **Value**: Number, the value of the cost.
- **CreationTS**: Integer, timestamp of when the SMS was created.
- **From**: String, the sender's information or number.
- **ID**: String, unique identifier for the SMS.
- **SmsCount**: Integer, the number of SMS messages included in the data.
- **Status**: Object containing:
  - **Code**: Number, the code representing the status.
  - **Description**: String, description of the status.
  - **Name**: String, name of the status.
- **To**: String, the recipient's information or number.

All fields are optional unless specified otherwise, and additional properties are allowed.