# Internal API Documentation

Welcome to the Lawbrokr internal API documentation.

This documentation covers all internal API endpoints, authentication flows, and integration guides for the engineering team.

## Base URLs

| Environment       | URL                                  |
| ----------------- | ------------------------------------ |
| Production        | `https://api.example.com/v1`         |
| Staging           | `https://staging-api.example.com/v1` |
| Local Development | `http://localhost:8000/v1`           |
| Mock Server       | `http://localhost:3000/v1`           |

## Quick Links

- [Authentication](Authentication.md) — How to obtain and use access tokens
- [Token Refresh](/token-refresh) — How the refresh token flow works
- [Error Handling](/error-handling) — Standard error response format and codes
- [Rate Limiting](/rate-limiting) — Request rate limits and throttling behavior

# Authentication

All API endpoints require authentication via a Bearer token in the `Authorization` header.

## Obtaining a Token

Send a `POST` request to `/v1/auth/token` with your credentials:

```json
{
  "email": "user@example.com",
  "password": "your-password"
}
```

json

A successful response includes an `access_token` (short-lived) and a `refresh_token` (long-lived):

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIs...",
  "refresh_token": "dGhpcyBpcyBhIHJlZnJl...",
  "token_type": "bearer",
  "expires_in": 900
}
```

json

## Using the Token

Include the access token in all subsequent requests:

```plaintext
Authorization: Bearer <access_token>
```

plaintext

## Token Expiration

Access tokens expire after **15 minutes** (900 seconds). When an access token expires, use the [Token Refresh](/token-refresh) flow to obtain a new one without re-authenticating.

## Error Responses

If a request is made without a valid token, the API returns a `401 Unauthorized` response:

```json
{
  "detail": "Invalid or expired access token.",
  "code": "AUTHENTICATION_REQUIRED"
}
```

json

# Token Refresh

When an access token expires, use the refresh token to obtain a new access token without requiring the user to re-enter their credentials.

## Refresh Flow

Send a `POST` request to `/v1/auth/token/refresh`:

```json
{
  "refresh_token": "dGhpcyBpcyBhIHJlZnJl..."
}
```

json

A successful response returns a new access token and a rotated refresh token:

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIs...",
  "refresh_token": "bmV3IHJlZnJlc2ggdG9r...",
  "token_type": "bearer",
  "expires_in": 900
}
```

json

## Important Notes

- Each refresh token can only be used **once**. After a successful refresh, the previous refresh token is invalidated and a new one is returned.
- If a refresh token has already been used or is expired, the API returns a `401 Unauthorized` response. The client must redirect the user to re-authenticate.
- Refresh tokens expire after **7 days** of inactivity.

# Error Handling

All API error responses follow a consistent JSON structure.

## Error Response Format

```json
{
  "detail": "A human-readable description of the error.",
  "code": "MACHINE_READABLE_ERROR_CODE"
}
```

json

| Field    | Type     | Description                                                      |
| -------- | -------- | ---------------------------------------------------------------- |
| `detail` | `string` | A human-readable message describing what went wrong.             |
| `code`   | `string` | A stable, machine-readable error code for programmatic handling. |

## Common Error Codes

| HTTP Status | Code                      | Description                                                       |
| ----------- | ------------------------- | ----------------------------------------------------------------- |
| `400`       | `VALIDATION_ERROR`        | The request body or parameters failed validation.                 |
| `401`       | `AUTHENTICATION_REQUIRED` | No valid access token was provided.                               |
| `403`       | `PERMISSION_DENIED`       | The authenticated user does not have permission for this action.  |
| `404`       | `RESOURCE_NOT_FOUND`      | The requested resource does not exist.                            |
| `409`       | `CONFLICT`                | The request conflicts with existing state (e.g. duplicate email). |
| `422`       | `UNPROCESSABLE_ENTITY`    | The request was well-formed but contained semantic errors.        |
| `429`       | `RATE_LIMIT_EXCEEDED`     | Too many requests. See [Rate Limiting](/rate-limiting).           |
| `500`       | `INTERNAL_ERROR`          | An unexpected server error occurred.                              |

## Validation Errors

For `400 VALIDATION_ERROR` responses, the `detail` field may contain additional context about which fields failed validation:

```json
{
  "detail": "Invalid value for 'email': must be a valid email address.",
  "code": "VALIDATION_ERROR"
}
```

json

# Rate Limiting

The API enforces rate limits to ensure fair usage and system stability.

## Default Limits

| Scope                                   | Limit         | Window     |
| --------------------------------------- | ------------- | ---------- |
| Authenticated requests                  | 1000 requests | Per minute |
| Authentication endpoints (`/v1/auth/*`) | 10 requests   | Per minute |

## Rate Limit Headers

Every API response includes rate limit information in the headers:

| Header                  | Description                                                   |
| ----------------------- | ------------------------------------------------------------- |
| `X-RateLimit-Limit`     | The maximum number of requests allowed in the current window. |
| `X-RateLimit-Remaining` | The number of requests remaining in the current window.       |
| `X-RateLimit-Reset`     | Unix timestamp (seconds) when the current window resets.      |

## Exceeding the Limit

When the rate limit is exceeded, the API returns a `429 Too Many Requests` response:

```json
{
  "detail": "Rate limit exceeded. Try again in 34 seconds.",
  "code": "RATE_LIMIT_EXCEEDED"
}
```

json

The `Retry-After` header is also included with the number of seconds to wait before retrying.

Login​Copy link
Authenticate a user and return tokens.

Sets the refresh token as an httpOnly cookie.

Body
required
application/json
Login credentials.

emailCopy link to email
Type:string · Email
Format:email
required
User's registered email address.

passwordCopy link to password
Type:string · Password
Format:password
write-only
required
User's password.

Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forpost/v1/auth/login
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/auth/login \
 --request POST \
 --header 'Content-Type: application/json' \
 --data '{
"email": "",
"password": ""
}'

Test Request
(post /v1/auth/login)
Status:200
Status:422
{
"access_token": "string",
"token_type": "bearer",
"user": {
"id": "123e4567-e89b-12d3-a456-426614174000",
"email": "hello@example.com",
"first_name": "string",
"last_name": "string",
"role": "string",
"is_active": true,
"law_firm_id": "123e4567-e89b-12d3-a456-426614174000",
"created_at": "2026-03-23T12:56:13.449Z"
}
}

Successful Response

Logout​Copy link
Clear the refresh token cookie.

Responses

200
Successful Response
application/json
Request Example forpost/v1/auth/logout
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/auth/logout \
 --request POST

Test Request
(post /v1/auth/logout)
Status:200
{
"message": "string"
}

Successful Response

Refresh Token​Copy link
Issue a new access token using the refresh cookie.

Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forpost/v1/auth/refresh
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/auth/refresh \
 --request POST

Test Request
(post /v1/auth/refresh)
Status:200
Status:422
{
"access_token": "string",
"token_type": "bearer"
}

Successful Response

Get My Profile​Copy link
Get the current user's profile.

Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forget/v1/users/me
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/users/me

Test Request
(get /v1/users/me)
Status:200
Status:422
{
"id": "123e4567-e89b-12d3-a456-426614174000",
"email": "hello@example.com",
"first_name": "string",
"last_name": "string",
"role": "string",
"is_active": true,
"law_firm_id": "123e4567-e89b-12d3-a456-426614174000",
"created_at": "2026-03-23T12:56:13.449Z"
}

Successful Response

Update My Profile​Copy link
Update the current user's profile.

Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Body
required
application/json
Request body for updating a user profile.

first_nameCopy link to first_name
Type:string · First Name
nullable
Updated first name.

last_nameCopy link to last_name
Type:string · Last Name
nullable
Updated last name.

Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forpatch/v1/users/me
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/users/me \
 --request PATCH \
 --header 'Content-Type: application/json' \
 --data '{
"first_name": "",
"last_name": ""
}'

Test Request
(patch /v1/users/me)
Status:200
Status:422
{
"id": "123e4567-e89b-12d3-a456-426614174000",
"email": "hello@example.com",
"first_name": "string",
"last_name": "string",
"role": "string",
"is_active": true,
"law_firm_id": "123e4567-e89b-12d3-a456-426614174000",
"created_at": "2026-03-23T12:56:13.449Z"
}

Successful Response

List users​Copy link
List users in the current law firm (admin+ only).

Query Parameters
pageCopy link to page
Type:integer · Page
min:  
1
Default
Integer numbers.

page_sizeCopy link to page_size
Type:integer · Page Size
min:  
1
max:  
100
Default
Integer numbers.

law_firm_idCopy link to law_firm_id
Type:string · Law Firm Id
Format:uuid
nullable
Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
X-Switch-LawFirmCopy link to X-Switch-LawFirm
Type:string · X-Switch-Lawfirm
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forget/v1/users
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/users

Test Request
(get /v1/users)
Status:200
Status:422
{
"users": [
{
"id": "123e4567-e89b-12d3-a456-426614174000",
"email": "hello@example.com",
"first_name": "string",
"last_name": "string",
"role": "string",
"is_active": true,
"law_firm_id": "123e4567-e89b-12d3-a456-426614174000",
"created_at": "2026-03-23T12:56:13.449Z"
}
],
"total": 1
}

Successful Response

Create user​Copy link
Create a new user in the current law firm (admin+ only).

Query Parameters
law_firm_idCopy link to law_firm_id
Type:string · Law Firm Id
Format:uuid
nullable
Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
X-Switch-LawFirmCopy link to X-Switch-LawFirm
Type:string · X-Switch-Lawfirm
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Body
required
application/json
Request body for creating a new user.

emailCopy link to email
Type:string · Email
Format:email
required
Email address for the new user (must be unique).

first_nameCopy link to first_name
Type:string · First Name
max length:  
100
required
User's first name.

last_nameCopy link to last_name
Type:string · Last Name
max length:  
100
required
User's last name.

passwordCopy link to password
Type:string · Password
min length:  
8
required
Password (minimum 8 characters).

roleCopy link to role
Type:string · Role
Default
User role: admin, member, or agency.

Responses

201
Successful Response
application/json

422
Validation Error
application/json
Request Example forpost/v1/users
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/users \
 --request POST \
 --header 'Content-Type: application/json' \
 --data '{
"email": "",
"password": "",
"first_name": "",
"last_name": "",
"role": "member"
}'

Test Request
(post /v1/users)
Status:201
Status:422
{
"id": "123e4567-e89b-12d3-a456-426614174000",
"email": "hello@example.com",
"first_name": "string",
"last_name": "string",
"role": "string",
"is_active": true,
"law_firm_id": "123e4567-e89b-12d3-a456-426614174000",
"created_at": "2026-03-23T12:56:13.449Z"
}

Successful Response

Get user by ID​Copy link
Get a specific user by ID (admin+ or self).

Path Parameters
user_idCopy link to user_id
Type:string · User Id
Format:uuid
required
Query Parameters
law_firm_idCopy link to law_firm_id
Type:string · Law Firm Id
Format:uuid
nullable
Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
X-Switch-LawFirmCopy link to X-Switch-LawFirm
Type:string · X-Switch-Lawfirm
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forget/v1/users/{user_id}
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/users/123e4567-e89b-12d3-a456-426614174000

Test Request
(get /v1/users/{user_id})
Status:200
Status:422
{
"id": "123e4567-e89b-12d3-a456-426614174000",
"email": "hello@example.com",
"first_name": "string",
"last_name": "string",
"role": "string",
"is_active": true,
"law_firm_id": "123e4567-e89b-12d3-a456-426614174000",
"created_at": "2026-03-23T12:56:13.449Z"
}

Successful Response

Get Platform Analytics Summary​Copy link
Get platform-wide analytics summary (super_admin only).

Query Parameters
start_dateCopy link to start_date
Type:string · Start Date
Format:date
required
full-date notation as defined by RFC 3339, section 5.6, for example, 2017-07-21

end_dateCopy link to end_date
Type:string · End Date
Format:date
required
full-date notation as defined by RFC 3339, section 5.6, for example, 2017-07-21

law_firm_idCopy link to law_firm_id
Type:string · Law Firm Id
Format:uuid
required
sourceCopy link to source
Type:string · Source
nullable
Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forget/v1/admin/analytics/summary
Shell Curl
curl 'https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/admin/analytics/summary?start_date=&end_date=&law_firm_id='

Test Request
(get /v1/admin/analytics/summary)
Status:200
Status:422
{
"date_range": [],
"total_leads": 1,
"total_spend": "string",
"cost_per_lead": "string",
"snapshots": [
{
"id": "123e4567-e89b-12d3-a456-426614174000",
"law_firm_id": "123e4567-e89b-12d3-a456-426614174000",
"date": "2026-03-23",
"metrics": {
"additionalProperty": "anything"
},
"source": "string",
"created_at": "2026-03-23T12:56:13.449Z"
}
]
}

Successful Response

List Analytics Snapshots​Copy link
List analytics snapshots with filters (super_admin only).

Query Parameters
start_dateCopy link to start_date
Type:string · Start Date
Format:date
required
full-date notation as defined by RFC 3339, section 5.6, for example, 2017-07-21

end_dateCopy link to end_date
Type:string · End Date
Format:date
required
full-date notation as defined by RFC 3339, section 5.6, for example, 2017-07-21

law_firm_idCopy link to law_firm_id
Type:string · Law Firm Id
Format:uuid
required
sourceCopy link to source
Type:string · Source
nullable
Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forget/v1/admin/analytics/snapshots
Shell Curl
curl 'https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/admin/analytics/snapshots?start_date=&end_date=&law_firm_id='

Test Request
(get /v1/admin/analytics/snapshots)
Status:200
Status:422
[
{
"id": "123e4567-e89b-12d3-a456-426614174000",
"law_firm_id": "123e4567-e89b-12d3-a456-426614174000",
"date": "2026-03-23",
"metrics": {
"additionalProperty": "anything"
},
"source": "string",
"created_at": "2026-03-23T12:56:13.449Z"
}
]

Successful Response

Generate Report​Copy link
Trigger an async report generation task (super_admin only).

Query Parameters
law_firm_idCopy link to law_firm_id
Type:string · Law Firm Id
Format:uuid
required
Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

202
Successful Response
application/json

422
Validation Error
application/json
Request Example forpost/v1/admin/analytics/generate-report
Shell Curl
curl 'https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/admin/analytics/generate-report?law_firm_id=' \
 --request POST

Test Request
(post /v1/admin/analytics/generate-report)
Status:202
Status:422
{
"additionalProperty": "string"
}

Successful Response

Get Law Firm Analytics​Copy link
Get specific law firm analytics (super_admin only).

Path Parameters
law_firm_idCopy link to law_firm_id
Type:string · Law Firm Id
Format:uuid
required
Query Parameters
start_dateCopy link to start_date
Type:string · Start Date
Format:date
required
full-date notation as defined by RFC 3339, section 5.6, for example, 2017-07-21

end_dateCopy link to end_date
Type:string · End Date
Format:date
required
full-date notation as defined by RFC 3339, section 5.6, for example, 2017-07-21

sourceCopy link to source
Type:string · Source
nullable
Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forget/v1/admin/analytics/{law_firm_id}
Shell Curl
curl 'https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/admin/analytics/123e4567-e89b-12d3-a456-426614174000?start_date=&end_date='

Test Request
(get /v1/admin/analytics/{law_firm_id})
Status:200
Status:422
{
"date_range": [],
"total_leads": 1,
"total_spend": "string",
"cost_per_lead": "string",
"snapshots": [
{
"id": "123e4567-e89b-12d3-a456-426614174000",
"law_firm_id": "123e4567-e89b-12d3-a456-426614174000",
"date": "2026-03-23",
"metrics": {
"additionalProperty": "anything"
},
"source": "string",
"created_at": "2026-03-23T12:56:13.449Z"
}
]
}

Successful Response

LEGACY

Login​Copy link
Authenticate a user and return tokens.

Sets the refresh token as an httpOnly cookie.

Body
required
application/json
Legacy login credentials.

emailCopy link to email
Type:string · Email
Format:email
required
User's registered email address.

passwordCopy link to password
Type:string · Password
Format:password
write-only
required
User's password.

Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forpost/v1/legacy/auth/login
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/legacy/auth/login \
 --request POST \
 --header 'Content-Type: application/json' \
 --data '{
"email": "",
"password": ""
}'

Test Request
(post /v1/legacy/auth/login)
Status:200
Status:422
{
"access_token": "string",
"token_type": "bearer",
"user": {
"id": 1,
"email": "string",
"first_name": "string",
"last_name": "string",
"role": 1,
"law_firm_id": 1
}
}

Successful Response

Logout​Copy link
Clear the refresh token cookie.

Responses

200
Successful Response
application/json
Request Example forpost/v1/legacy/auth/logout
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/legacy/auth/logout \
 --request POST

Test Request
(post /v1/legacy/auth/logout)
Status:200
{
"message": "string"
}

Successful Response

Refresh token​Copy link
Issue a new access token using the refresh cookie.

Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forpost/v1/legacy/auth/refresh
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/legacy/auth/refresh \
 --request POST

Test Request
(post /v1/legacy/auth/refresh)
Status:200
Status:422
{
"access_token": "string",
"token_type": "bearer"
}

Successful Response
Get current user​Copy link
Return the current user's profile.

Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forget/v1/legacy/auth/me
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/legacy/auth/me

Test Request
(get /v1/legacy/auth/me)
Status:200
Status:422
{
"id": 1,
"email": "string",
"first_name": "string",
"last_name": "string",
"role": 1,
"law_firm_id": 1
}

Successful Response

Analytics counts​Copy link
Retrieve aggregate analytics across all accounts.

Returns total visits, total conversions, and conversion rate for the specified date range, excluding demo accounts. Optionally filter to a single law firm by providing law_firm_id.

Query Parameters
start_dateCopy link to start_date
Type:string · Start Date
Format:date
required
Start date for the analytics period (inclusive).

end_dateCopy link to end_date
Type:string · End Date
Format:date
required
End date for the analytics period (inclusive).

law_firm_idCopy link to law_firm_id
Type:integer · Law Firm Id
nullable
Optional law firm ID to filter analytics to a single firm.

Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forget/v1/legacy/admin/analytics/counts
Shell Curl
curl 'https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/legacy/admin/analytics/counts?start_date=&end_date='

Test Request
(get /v1/legacy/admin/analytics/counts)
Status:200
Status:422
{
"total_visits": 1,
"total_conversions": 1,
"conversion_rate": 1
}

Successful Response

Month-over-month analytics​Copy link
Compare analytics across two rolling 30-day windows.

Returns visit counts, conversion counts, conversion rates, and percentage changes between the current 30-day period and the previous 30-day period, excluding demo accounts.

Query Parameters
law_firm_idCopy link to law_firm_id
Type:integer · Law Firm Id
nullable
Optional law firm ID to filter analytics to a single firm.

Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forget/v1/legacy/admin/analytics/mom
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/legacy/admin/analytics/mom

Test Request
(get /v1/legacy/admin/analytics/mom)
Status:200
Status:422
{
"current_period_start": "2026-03-23",
"current_period_end": "2026-03-23",
"previous_period_start": "2026-03-23",
"previous_period_end": "2026-03-23",
"current_visits": 1,
"current_conversions": 1,
"current_conversion_rate": 1,
"previous_visits": 1,
"previous_conversions": 1,
"previous_conversion_rate": 1,
"visits_change": 1,
"conversions_change": 1,
"conversion_rate_change": 1
}

Successful Response

Chart leads​Copy link
Return daily visits and conversions for a date range.

Every day in the range is included — days with no leads return zeros. Excludes demo accounts. Optionally filter to a single law firm.

Query Parameters
start_dateCopy link to start_date
Type:string · Start Date
Format:date
required
Start date for the chart range (inclusive).

end_dateCopy link to end_date
Type:string · End Date
Format:date
required
End date for the chart range (inclusive).

law_firm_idCopy link to law_firm_id
Type:integer · Law Firm Id
nullable
Optional law firm ID to filter analytics to a single firm.

Headers
authorizationCopy link to authorization
Type:string · Authorization
nullable
Cookies
refresh_tokenCopy link to refresh_token
Type:string · Refresh Token
nullable
Responses

200
Successful Response
application/json

422
Validation Error
application/json
Request Example forget/v1/legacy/admin/analytics/chart/leads
Shell Curl
curl 'https://lawbrokr-lawbrokr-backend.apidocumentation.com/v1/legacy/admin/analytics/chart/leads?start_date=&end_date='

Test Request
(get /v1/legacy/admin/analytics/chart/leads)
Status:200
Status:422
{
"data": [
{
"date": "2026-03-23",
"visits": 1,
"conversions": 1
}
]
}

Successful Response

Health check​Copy link
Health check endpoint for load balancer probes.

Responses

200
Successful Response
application/json
Request Example forget/healthz
Shell Curl
curl https://lawbrokr-lawbrokr-backend.apidocumentation.com/healthz

Test Request
(get /healthz)
Status:200
{
"additionalProperty": "string"
}

Successful Response

MODELS

AdminAnalyticsChartLeadsItem
Single day of lead chart data.

conversions
Type:integer · Conversions
required
Number of leads that completed intake on this day.

date
Type:string · Date
Format:date
required
Calendar date for this data point.

visits
Type:integer · Visits
required
Number of leads created on this day.

AdminAnalyticsChartLeadsResponse
Daily visits and conversions over a date range.

data
Type:array object[] · Data
required
One entry per date in the requested range, ordered ascending.

Single day of lead chart data.

conversions
Type:integer · Conversions
required
Number of leads that completed intake on this day.

date
Type:string · Date
Format:date
required
Calendar date for this data point.

visits
Type:integer · Visits
required
Number of leads created on this day.
AdminAnalyticsCountsResponse
Aggregate analytics summary across all non-demo accounts.

conversion_rate
Type:number · Conversion Rate
required
Percentage of visits that converted, rounded to the nearest integer.

total_conversions
Type:integer · Total Conversions
required
Number of leads that completed intake in the date range.

total_visits
Type:integer · Total Visits
required
Total number of leads (visits) in the date range.

AdminAnalyticsMomResponse
Month-over-month analytics comparison between two rolling 30-day windows.

conversion_rate_change
Type:number · Conversion Rate Change
required
Percentage change in conversion rate between periods.

conversions_change
Type:number · Conversions Change
required
Percentage change in conversions between periods.

current_conversion_rate
Type:number · Current Conversion Rate
required
Conversion rate in the current period (percentage).

current_conversions
Type:integer · Current Conversions
required
Total conversions in the current period.

current_period_end
Type:string · Current Period End
Format:date
required
Last day of the current 30-day window.

current_period_start
Type:string · Current Period Start
Format:date
required
First day of the current 30-day window.

current_visits
Type:integer · Current Visits
required
Total visits in the current period.

previous_conversion_rate
Type:number · Previous Conversion Rate
required
Conversion rate in the previous period (percentage).

previous_conversions
Type:integer · Previous Conversions
required
Total conversions in the previous period.

previous_period_end
Type:string · Previous Period End
Format:date
required
Last day of the previous 30-day window.

previous_period_start
Type:string · Previous Period Start
Format:date
required
First day of the previous 30-day window.

previous_visits
Type:integer · Previous Visits
required
Total visits in the previous period.

visits_change
Type:number · Visits Change
required
Percentage change in visits between periods.

AnalyticsSnapshotResponse
Single analytics snapshot.

created_at
Type:string · Created At
Format:date-time
required
When this snapshot was recorded.

date
Type:string · Date
Format:date
required
Date of the analytics snapshot.

id
Type:string · Id
Format:uuid
required
Snapshot identifier.

law_firm_id
Type:string · Law Firm Id
Format:uuid
required
Law firm this snapshot belongs to.

metrics
Type:object · Metrics
required
Key-value metrics for this snapshot.

propertyName
Type:anything
source
Type:string · Source
required
Analytics data source (e.g., 'google_ads', 'lsa').

AnalyticsSummaryResponse
Aggregated analytics summary over a date range.

cost_per_lead
Type:string · Cost Per Lead
Pattern:^(?!^[-+.]_$)[+-]?0_\d*\.?\d*$
required
Average cost per lead (total_spend / total_leads).

date_range
Type:array · Date Range
2…2
required
Start and end dates of the summary period.

snapshots
Type:array object[] · Snapshots
required
Individual daily snapshots.

Single analytics snapshot.

created_at
Type:string · Created At
Format:date-time
required
When this snapshot was recorded.

date
Type:string · Date
Format:date
required
Date of the analytics snapshot.

id
Type:string · Id
Format:uuid
required
Snapshot identifier.

law_firm_id
Type:string · Law Firm Id
Format:uuid
required
Law firm this snapshot belongs to.

metrics
Type:object · Metrics
required
Key-value metrics for this snapshot.

Metrics
source
Type:string · Source
required
Analytics data source (e.g., 'google_ads', 'lsa').

total_leads
Type:integer · Total Leads
required
Total number of leads in the period.

total_spend
Type:string · Total Spend
Pattern:^(?!^[-+.]_$)[+-]?0_\d*\.?\d*$
required
Total advertising spend in the period.

HTTPValidationError
detail
Type:array object[] · Detail
loc
Type:array · Location
required

loc
msg
Type:string · Message
required
type
Type:string · Error Type
required
ctx
Type:object · Context
input

LegacyLoginRequest
Legacy login credentials.

email
Type:string · Email
Format:email
required
User's registered email address.

password
Type:string · Password
Format:password
write-only
required
User's password.

LegacyLoginResponse
Successful legacy login response with tokens and user info.

access_token
Type:string · Access Token
required
Short-lived JWT access token.

user
Type:object · LegacyUserResponse
required
Authenticated legacy user's profile.

email
Type:string · Email
required
User's email address.

first_name
Type:string · First Name
nullable
required
User's first name.

id
Type:integer · Id
required
Legacy integer user ID.

last_name
Type:string · Last Name
nullable
required
User's last name.

law_firm_id
Type:integer · Law Firm Id
nullable
required
Legacy integer law firm ID.

role
Type:integer · Role
nullable
required
Legacy numeric role identifier.

token_type
Type:string · Token Type
Default
OAuth2 token type.

LegacyTokenRefreshResponse
Response for legacy token refresh endpoint.

access_token
Type:string · Access Token
required
New short-lived JWT access token.

token_type
Type:string · Token Type
Default
OAuth2 token type.

LegacyUserResponse
Public legacy user representation.

email
Type:string · Email
required
User's email address.

first_name
Type:string · First Name
nullable
required
User's first name.

id
Type:integer · Id
required
Legacy integer user ID.

last_name
Type:string · Last Name
nullable
required
User's last name.

law_firm_id
Type:integer · Law Firm Id
nullable
required
Legacy integer law firm ID.

role
Type:integer · Role
nullable
required
Legacy numeric role identifier.

LoginRequest
Login credentials.

email
Type:string · Email
Format:email
required
User's registered email address.

password
Type:string · Password
Format:password
write-only
required
User's password.

LoginResponse
Successful login response with tokens and user info.

access_token
Type:string · Access Token
required
Short-lived JWT access token (15 min).

user
Type:object · UserResponse
required
Authenticated user's profile.

created_at
Type:string · Created At
Format:date-time
required
Account creation timestamp.

email
Type:string · Email
Format:email
required
User's email address.

first_name
Type:string · First Name
required
User's first name.

id
Type:string · Id
Format:uuid
required
Unique user identifier.

is_active
Type:boolean · Is Active
required
Whether the user account is active.

last_name
Type:string · Last Name
required
User's last name.

law_firm_id
Type:string · Law Firm Id
Format:uuid
nullable
required
ID of the user's law firm, if assigned.

role
Type:string · Role
required
User role: super_admin, admin, member, or agency.

token_type
Type:string · Token Type
Default
OAuth2 token type.

MessageResponse
Simple response with a status message.

message
Type:string · Message
required
Human-readable status message.

TokenRefreshResponse
Response for token refresh endpoint.

access_token
Type:string · Access Token
required
New short-lived JWT access token (15 min).

token_type
Type:string · Token Type
Default
OAuth2 token type.

UserCreateRequest
Request body for creating a new user.

email
Type:string · Email
Format:email
required
Email address for the new user (must be unique).

first_name
Type:string · First Name
max length:  
100
required
User's first name.

last_name
Type:string · Last Name
max length:  
100
required
User's last name.

password
Type:string · Password
min length:  
8
required
Password (minimum 8 characters).

role
Type:string · Role
Default
User role: admin, member, or agency.

UserListResponse
Paginated list of users.

total
Type:integer · Total
required
Total number of users matching the query.

users
Type:array object[] · Users
required
List of user records.

Public user representation.

created_at
Type:string · Created At
Format:date-time
required
Account creation timestamp.

email
Type:string · Email
Format:email
required
User's email address.

first_name
Type:string · First Name
required
User's first name.

id
Type:string · Id
Format:uuid
required
Unique user identifier.

is_active
Type:boolean · Is Active
required
Whether the user account is active.

last_name
Type:string · Last Name
required
User's last name.

law_firm_id
Type:string · Law Firm Id
Format:uuid
nullable
required
ID of the user's law firm, if assigned.

role
Type:string · Role
required
User role: super_admin, admin, member, or agency.

UserResponse
Public user representation.

created_at
Type:string · Created At
Format:date-time
required
Account creation timestamp.

email
Type:string · Email
Format:email
required
User's email address.

first_name
Type:string · First Name
required
User's first name.

id
Type:string · Id
Format:uuid
required
Unique user identifier.

is_active
Type:boolean · Is Active
required
Whether the user account is active.

last_name
Type:string · Last Name
required
User's last name.

law_firm_id
Type:string · Law Firm Id
Format:uuid
nullable
required
ID of the user's law firm, if assigned.

role
Type:string · Role
required
User role: super_admin, admin, member, or agency.

UserUpdateRequest
Request body for updating a user profile.

first_name
Type:string · First Name
nullable
Updated first name.

last_name
Type:string · Last Name
nullable
Updated last name.

ValidationError
loc
Type:array · Location
required

Any of
string
Type:string
msg
Type:string · Message
required
type
Type:string · Error Type
required
ctx
Type:object · Context
input
