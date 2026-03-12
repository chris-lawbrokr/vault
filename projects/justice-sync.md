# Justice Sync — AI Backend

> AI-powered law firm analytics and marketing assistant. FastAPI backend for Justice Frontend.

## Tech Stack
- FastAPI, Python 3.12
- PostgreSQL (SQLAlchemy + pg8000/asyncpg)
- Redis (sessions)
- Google Cloud Vertex AI — Gemini 3 Pro/Flash
- Google ADK (Agent Development Kit)
- Docker / Docker Swarm (2 replicas)

## Running Locally
```bash
uv run uvicorn app.app:app --host 0.0.0.0 --port 8000
```

---

## API Endpoints

### Authentication (`/auth`)
| Method | Path | Description |
|--------|------|-------------|
| POST | `/auth/login` | SSO token login (AES-256-GCM), creates Redis session |
| POST | `/auth/logout` | Destroy session |
| GET | `/auth/session` | Check session validity |
| POST | `/auth/refresh` | Extend session TTL (7-day sliding) |

### Users (`/users`)
| Method | Path | Description |
|--------|------|-------------|
| GET | `/users/me` | Authenticated user profile |

### Connections (`/connections`)
| Method | Path | Description |
|--------|------|-------------|
| POST | `/connections/google-ads` | Store encrypted Google Ads credentials |
| POST | `/connections/meta-ads` | Store encrypted Meta Ads credentials |
| POST | `/connections/callrail` | Store encrypted CallRail credentials |
| PUT | `/connections/` | Bulk update connections |
| DELETE | `/connections/` | Delete connection type |
| GET | `/connections/status/{law_firm_id}` | Check connection flags |
| GET | `/connections/free-trial/{law_firm_id}` | Trial status |

### Chat (`/chat`)
| Method | Path | Description |
|--------|------|-------------|
| POST | `/chat/message` | Send message to AI agent (enforces trial limits) |

### Chat History (`/chat-history`)
| Method | Path | Description |
|--------|------|-------------|
| GET | `/chat-history/conversations` | List conversations (cursor pagination) |
| POST | `/chat-history/conversations` | Create new conversation |
| GET | `/chat-history/conversations/{id}/messages` | Get messages (paginated) |
| POST | `/chat-history/conversations/{id}/messages/stream` | Stream AI response (SSE) |

### Ad Platforms
- `/google-ads/` — Google Ads data (placeholder for direct API)
- `/meta-ads/` — Meta campaigns, ad sets, targeting, insights, metrics
- `/callrail/` — Call tracking (placeholder)
- `/semrush/` — DataForSEO/SEMrush (placeholder)

---

## Authentication & Security

### SSO Tokens
- AES-256-GCM encryption with PBKDF2 key derivation
- Token format: `Base64url(IV[12] + Encrypted + AuthTag[16])`
- Default expiration: 300s (5 min)

### Sessions
- Redis-backed, 256-bit session IDs
- HTTP-only secure cookies (`justice_session`)
- 7-day sliding window expiration

### Credential Storage
- Fernet symmetric encryption for all stored API credentials

---

## AI Agent System

**Models**: Gemini 3 Pro (agent reasoning), Gemini 3 Flash (user messages)

### Agent Tools (40+)

| Category | Tools |
|----------|-------|
| Analytics | `tool_get_law_firm_information`, `tool_get_justice_law_firm_analytics`, funnel analytics, workflow analytics |
| Google Ads (18) | Campaign summaries, keywords, device/location metrics, search terms, budgets |
| Meta Ads (9) | Performance, campaigns, ad sets, targeting, ad-level metrics |
| CallRail (4) | Call analytics, call info, call counts, durations |
| DataForSEO (4) | Keyword metrics, practice area discovery, location codes |
| Connections (4) | Get account IDs and API keys |

### Chat Modes
- **Synchronous**: Full response returned
- **Streaming (SSE)**: Events — `text_chunk`, `tool_call`, `user_message`, `complete`, `error`

---

## Database Schema (30+ tables, 6 schemas)

### `justice` schema
- `law_firms` — id, name, primary_location, practice_area[]
- `users` — id, law_firm_id, name, email, access_token
- `funnels` — id, law_firm_id, name, published
- `workflows` — id, law_firm_id, funnel_id, name, published
- `free_trial_status` — law_firm_id, remaining_messages (0-10 trial, -1 paid)

### `google_ads` schema
- `customers`, `campaigns`, `campaign_budgets`, `ad_groups`, `keywords`, `campaign_locations`, `ads`
- `campaign_metrics_daily`, `campaign_metrics_by_device`, `campaign_metrics_by_location`
- `search_terms`, `lawfirm_campaigns`

### `meta_ads` schema
- `campaigns`, `adsets`, `adset_targeting`
- `campaign_metrics_daily`, `ad_metrics_daily`, `lawfirm_campaigns`

### `callrail` schema
- `users`, `milestones`, `sync_metadata`, `calls`

### `chat_history` schema
- `conversations` (UUID), `messages` (UUID, role: user/justice)

### `connections` schema
- `law_firm_connections` — encrypted credentials for all services

---

## Background Jobs

### Daily (00:00 UTC)
- `sync_internal`, `sync_callrail_db`, `sync_meta_to_db`, `sync_google_ads_db`

### Weekly (Sunday 01:00 UTC)
- `sync_dfs_to_db`

---

## Free Trial System
- New users: 10 free messages
- Each AI message decrements counter
- Paid users: `remaining_messages = -1` (unlimited)
- Enforced at `/chat/message` endpoint

---

## Key Dependencies
FastAPI 0.120+, SQLAlchemy 2.0+, Pydantic 2.7+, Redis 5.0+, Google Ads API 28.0+, Meta Business SDK 23.0+, Google GenAI 1.1+, Google ADK 1.22.0, APScheduler 3.11+, Sentry SDK, Loguru

## Related Files
- [[justice-frontend]] — Next.js frontend
