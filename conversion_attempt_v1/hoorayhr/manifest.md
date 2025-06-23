# Overview

This document provides a configuration guide for integrating with a generic third-party human resources API using the dltHub REST API source format. The integration supports retrieving data related to users, time-off, leave types, and sick leaves. The API requires authentication and supports multiple resources with specific endpoints.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "time_off",
            "endpoint": {
                "path": "/v1/time-off",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "leave_types",
            "endpoint": {
                "path": "/v1/leave-types",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "sick_leaves",
            "endpoint": {
                "path": "/v1/sick-leave",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the request header under the name "Authorization".

## Resources

### Users
- **Endpoint Path**: `/v1/users`
- **HTTP Method**: GET
- **Pagination**: Single page
- **Data Selector**: `data`
- **Primary Key**: `id`

### Time Off
- **Endpoint Path**: `/v1/time-off`
- **HTTP Method**: GET
- **Pagination**: Single page
- **Data Selector**: `data`
- **Primary Key**: `id`

### Leave Types
- **Endpoint Path**: `/v1/leave-types`
- **HTTP Method**: GET
- **Pagination**: Single page
- **Data Selector**: `data`
- **Primary Key**: `id`

### Sick Leaves
- **Endpoint Path**: `/v1/sick-leave`
- **HTTP Method**: GET
- **Pagination**: Single page
- **Data Selector**: `data`
- **Primary Key**: `id`

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP status codes such as 429 (Too Many Requests) and 500 (Internal Server Error) with retries.
- **Backoff Strategy**: Use exponential backoff for retries to avoid overwhelming the server.

## Schema

### Users
- **Fields**: Includes `id`, `city`, `email`, `phone`, `teams`, `avatar`, `gender`, `locale`, `status`, `country`, `isAdmin`, `zipcode`, `initials`, `jobTitle`, `lastName`, `nickName`, `timezone`, `abilities`, `biography`, `birthdate`, `companyId`, `createdAt`, `firstName`, `insertion`, `invitedAt`, `updatedAt`, `birthPlace`, `civilStatus`, `costCenters`, `nationality`, `onBoardedAt`, `birthCountry`, `emailPrivate`, `integrations`, `addressNumber`, `addressStreet`, `emergencyName`, `holidayPolicy`, `lastNameUsage`, `companyEndDate`, `employeeNumber`, `addressAddition`, `holidayPolicyId`, `invitedByUserId`, `travelAllowance`, `companyEndReason`, `companyStartDate`, `inviteAcceptedAt`, `inviteRemindedAt`, `bankAccountNumber`, `emergencyRelation`, `emergencyWorkPhone`, `citizenServiceNumber`, `emergencyPersonalPhone`, `twoFactorAuthentication`, `bankAccountNumberOnBehalfOf`.
- **Types**: Various types including `string`, `number`, `array`, and `object`.

### Time Off
- **Fields**: Includes `id`, `end`, `notes`, `pause`, `reply`, `start`, `labels`, `status`, `userId`, `timezone`, `createdAt`, `holidayId`, `isPrivate`, `leaveUnit`, `updatedAt`, `budgetTotal`, `leaveTypeId`, `timeOffType`, `userIdApproved`, `baseTimeOffType`, `isNotCalculated`, `leaveTypeRuleId`, `budgetAdjustment`, `budgetCalculated`.
- **Types**: Various types including `string`, `number`, `array`, and `object`.

### Leave Types
- **Fields**: Includes `id`, `icon`, `name`, `color`, `budget`, `default`, `isLegacy`, `createdAt`, `updatedAt`, `updatedBy`, `leaveInDays`, `unpaidLeave`, `periodOffset`, `leaveTypeRules`, `autoApproveLimit`, `subtractHolidays`, `calculationMethod`, `budgetReleaseTiming`, `invisibleInCalendar`, `budgetReleaseRecurrence`, `leaveTypeSystemCategory`, `accumulateBudgetWhenAbsent`.
- **Types**: Various types including `string`, `number`, `array`, and `object`.

### Sick Leaves
- **Fields**: Includes `id`, `notes`, `status`, `userId`, `timezone`, `createdAt`, `updatedAt`, `percentage`, `actualStart`, `actualTotal`, `actualReturn`, `expectedTotal`, `reportedStart`, `actualStartEnd`, `expectedReturn`, `reportedReturn`, `userIdReported`, `actualReturnEnd`, `userIdConfirmed`.
- **Types**: Various types including `string`, `number`, `array`, and `object`.