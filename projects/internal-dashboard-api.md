# Internal Dashboard API

> Lawbrokr internal backend API — FastAPI service powering the internal dashboard.

## Base URLs

| Environment | URL |
|---|---|
| Production | `https://api.example.com/v1` |
| Staging | `https://staging-api.example.com/v1` |
| Local | `http://localhost:8000/v1` |
| Mock | `http://localhost:3000/v1` |

## Authentication

Bearer token via `Authorization` header. Refresh token stored as `httpOnly` cookie.

- **Access token**: JWT, expires in **15 minutes** (900s)
- **Refresh token**: single-use, rotated on each refresh, expires after **7 days** of inactivity
- All authenticated endpoints accept optional `Authorization` header + `refresh_token` cookie

## Rate Limits

| Scope | Limit | Window |
|---|---|---|
| Authenticated requests | 1000 | per minute |
| Auth endpoints (`/v1/auth/*`) | 10 | per minute |

Response headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset` (unix timestamp). Returns `429` with `Retry-After` header when exceeded.

## Error Format

All errors return:
```json
{ "detail": "Human-readable message", "code": "MACHINE_READABLE_CODE" }
```

| Status | Code | Meaning |
|---|---|---|
| 400 | `VALIDATION_ERROR` | Request body/params failed validation |
| 401 | `AUTHENTICATION_REQUIRED` | Missing or invalid access token |
| 403 | `PERMISSION_DENIED` | Insufficient permissions |
| 404 | `RESOURCE_NOT_FOUND` | Resource does not exist |
| 409 | `CONFLICT` | Conflicts with existing state (e.g. duplicate email) |
| 422 | `UNPROCESSABLE_ENTITY` | Well-formed but semantic errors |
| 429 | `RATE_LIMIT_EXCEEDED` | Too many requests |
| 500 | `INTERNAL_ERROR` | Unexpected server error |

---

## Endpoints

### Auth

#### `POST /v1/auth/login` — Login

Authenticate user, return access token + set refresh cookie.

**Body:**
| Field | Type | Required | Description |
|---|---|---|---|
| `email` | string (email) | yes | Registered email |
| `password` | string (password) | yes | User password |

**200 Response:**
```json
{
  "access_token": "eyJ...",
  "token_type": "bearer",
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "first_name": "string",
    "last_name": "string",
    "role": "admin|member|agency|super_admin",
    "is_active": true,
    "law_firm_id": "uuid|null",
    "created_at": "2026-03-23T12:56:13.449Z"
  }
}
```

#### `POST /v1/auth/logout` — Logout

Clears the refresh token cookie.

**200 Response:** `{ "message": "string" }`

#### `POST /v1/auth/refresh` — Refresh Token

Issue new access token using refresh cookie. Previous refresh token is invalidated; new one is returned via cookie.

**200 Response:**
```json
{ "access_token": "eyJ...", "token_type": "bearer" }
```

---

### Users

#### `GET /v1/users/me` — Get My Profile

Returns the authenticated user's profile.

**200 Response:** `UserResponse` (see Models)

#### `PATCH /v1/users/me` — Update My Profile

**Body:**
| Field | Type | Required | Description |
|---|---|---|---|
| `first_name` | string | no | Updated first name |
| `last_name` | string | no | Updated last name |

**200 Response:** `UserResponse`

#### `GET /v1/users` — List Users

List users in current law firm. **Admin+ only.**

**Query params:**
| Param | Type | Default | Description |
|---|---|---|---|
| `page` | integer (min 1) | 1 | Page number |
| `page_size` | integer (1–100) | default | Items per page |
| `law_firm_id` | uuid | null | Filter by law firm |

**Headers:** `X-Switch-LawFirm` (optional, uuid) — switch law firm context

**200 Response:**
```json
{ "users": [UserResponse, ...], "total": 1 }
```

#### `POST /v1/users` — Create User

Create a new user in current law firm. **Admin+ only.**

**Query params:** `law_firm_id` (uuid, optional)
**Headers:** `X-Switch-LawFirm` (optional)

**Body:**
| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `email` | string (email) | yes | unique | Email address |
| `first_name` | string | yes | max 100 | First name |
| `last_name` | string | yes | max 100 | Last name |
| `password` | string | yes | min 8 | Password |
| `role` | string | no | default: `member` | `admin`, `member`, or `agency` |

**201 Response:** `UserResponse`

#### `GET /v1/users/{user_id}` — Get User by ID

Get a specific user. **Admin+ or self.**

**Path params:** `user_id` (uuid, required)
**Query params:** `law_firm_id` (uuid, optional)
**Headers:** `X-Switch-LawFirm` (optional)

**200 Response:** `UserResponse`

---

### Admin Analytics (super_admin only)

#### `GET /v1/admin/analytics/summary` — Platform Analytics Summary

**Query params:**
| Param | Type | Required | Description |
|---|---|---|---|
| `start_date` | date (RFC 3339) | yes | Period start |
| `end_date` | date (RFC 3339) | yes | Period end |
| `law_firm_id` | uuid | yes | Law firm to query |
| `source` | string | no | Filter by source (e.g. `google_ads`, `lsa`) |

**200 Response:** `AnalyticsSummaryResponse`
```json
{
  "date_range": ["2026-03-01", "2026-03-23"],
  "total_leads": 42,
  "total_spend": "1500.00",
  "cost_per_lead": "35.71",
  "snapshots": [AnalyticsSnapshotResponse, ...]
}
```

#### `GET /v1/admin/analytics/snapshots` — List Analytics Snapshots

Same query params as summary. Returns array of `AnalyticsSnapshotResponse`.

#### `GET /v1/admin/analytics/{law_firm_id}` — Law Firm Analytics

**Path params:** `law_firm_id` (uuid, required)
**Query params:** `start_date`, `end_date` (required), `source` (optional)

**200 Response:** `AnalyticsSummaryResponse` (same shape as summary)

#### `POST /v1/admin/analytics/generate-report` — Generate Report

Trigger async report generation.

**Query params:** `law_firm_id` (uuid, required)

**202 Response:** `{ "additionalProperty": "string" }`

---

### Health

#### `GET /healthz` — Health Check

Load balancer probe. No auth required.

**200 Response:** `{ "additionalProperty": "string" }`

---

## Legacy Endpoints

> Legacy API uses **integer IDs** instead of UUIDs and has a different response shape. Prefer v1 endpoints for new development.

### Legacy Auth

| Method | Path | Description |
|---|---|---|
| `POST` | `/v1/legacy/auth/login` | Login (returns integer-ID user) |
| `POST` | `/v1/legacy/auth/logout` | Logout (clear cookie) |
| `POST` | `/v1/legacy/auth/refresh` | Refresh token |
| `GET` | `/v1/legacy/auth/me` | Get current user profile |

**Legacy login response shape:**
```json
{
  "access_token": "string",
  "token_type": "bearer",
  "user": {
    "id": 1,
    "email": "string",
    "first_name": "string|null",
    "last_name": "string|null",
    "role": 1,
    "law_firm_id": 1
  }
}
```

### Legacy Analytics

#### `GET /v1/legacy/admin/analytics/counts` — Analytics Counts

Aggregate analytics across all non-demo accounts.

**Query params:** `start_date` (date, required), `end_date` (date, required), `law_firm_id` (integer, optional)

**200 Response:**
```json
{ "total_visits": 100, "total_conversions": 25, "conversion_rate": 25 }
```

#### `GET /v1/legacy/admin/analytics/mom` — Month-over-Month

Compare two rolling 30-day windows. Excludes demo accounts.

**Query params:** `law_firm_id` (integer, optional)

**200 Response:**
```json
{
  "current_period_start": "2026-02-21",
  "current_period_end": "2026-03-23",
  "previous_period_start": "2026-01-22",
  "previous_period_end": "2026-02-20",
  "current_visits": 120,
  "current_conversions": 30,
  "current_conversion_rate": 25.0,
  "previous_visits": 100,
  "previous_conversions": 20,
  "previous_conversion_rate": 20.0,
  "visits_change": 20.0,
  "conversions_change": 50.0,
  "conversion_rate_change": 25.0
}
```

#### `GET /v1/legacy/admin/analytics/chart/leads` — Chart Leads

Daily visits and conversions. Every day in range included (zeros for days with no data). Excludes demo accounts.

**Query params:** `start_date` (date, required), `end_date` (date, required), `law_firm_id` (integer, optional)

**200 Response:**
```json
{
  "data": [
    { "date": "2026-03-23", "visits": 10, "conversions": 3 }
  ]
}
```

---

## Models

### UserResponse

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | uuid | yes | Unique user identifier |
| `email` | string (email) | yes | Email address |
| `first_name` | string | yes | First name |
| `last_name` | string | yes | Last name |
| `role` | string | yes | `super_admin`, `admin`, `member`, or `agency` |
| `is_active` | boolean | yes | Account active status |
| `law_firm_id` | uuid (nullable) | yes | Associated law firm |
| `created_at` | datetime | yes | Account creation timestamp |

### AnalyticsSnapshotResponse

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | uuid | yes | Snapshot identifier |
| `law_firm_id` | uuid | yes | Law firm this belongs to |
| `date` | date | yes | Snapshot date |
| `metrics` | object | yes | Key-value metrics (flexible schema) |
| `source` | string | yes | Data source (e.g. `google_ads`, `lsa`) |
| `created_at` | datetime | yes | When recorded |

### AnalyticsSummaryResponse

| Field | Type | Required | Description |
|---|---|---|---|
| `date_range` | array (2 items) | yes | Start and end dates |
| `total_leads` | integer | yes | Total leads in period |
| `total_spend` | string (decimal) | yes | Total ad spend |
| `cost_per_lead` | string (decimal) | yes | Spend / leads |
| `snapshots` | AnalyticsSnapshotResponse[] | yes | Daily snapshots |

### LegacyUserResponse

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Legacy integer user ID |
| `email` | string | yes | Email address |
| `first_name` | string (nullable) | yes | First name |
| `last_name` | string (nullable) | yes | Last name |
| `role` | integer (nullable) | yes | Legacy numeric role |
| `law_firm_id` | integer (nullable) | yes | Legacy integer law firm ID |

---

## Notes

- See [internal-dashboard.md](internal-dashboard.md) for Figma designs
- Terminology: "Funnel" = Campaign, "Workflow" = Journey, "Marketing Source" = Source
- Legacy endpoints use integer IDs; v1 endpoints use UUIDs
- The `X-Switch-LawFirm` header allows admin users to operate in another law firm's context
- All date params use RFC 3339 `full-date` format: `YYYY-MM-DD`
