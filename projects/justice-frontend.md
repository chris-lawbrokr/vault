# Justice Frontend — AI Chat UI

> AI-powered legal marketing assistant web app. Next.js frontend for Justice Sync.

## Tech Stack
- Next.js 15.5.9, React 19.1.0, TypeScript 5.x
- Tailwind CSS 4.x (Oklch color space)
- react-markdown + remark-gfm
- Radix UI, Class Variance Authority (CVA)
- SendGrid (upgrade emails)

---

## Features

### Chat Interface
- Real-time streaming via SSE
- Dual model: "Quick Response" (gemini-3-flash) / "Deeper Thinking" (gemini-3-pro)
- Markdown rendering (tables, lists, code blocks)
- Quick prompts on empty chat, follow-up suggestions after responses
- Auto-scrolling, auto-expanding textarea, loading indicator

### Conversation Management
- Create, list (up to 50), switch conversations
- Conversation ID synced to URL (`?conversation_id=...`)
- Infinite scroll pagination, auto-generated titles
- Session caching of conversation list

### Authentication
- SSO token login via URL parameter (`?sso_token=...`)
- Session cookie (`justice_session`), auto-login on page load
- Auth error page → redirect to lawbrokr.com
- Dev login at `/api/auth/dev-login`

### Settings Page (3 tabs)
- **Profile**: read-only display (name, email, account type)
- **Connections**: Google Ads, Meta Ads, CallRail — add/edit/remove with validation
- **Help**: placeholder

### Free Trial / Upgrade
- Detects 403 (trial limit), shows upgrade prompt
- Sends upgrade request email via SendGrid

---

## API Routes (Next.js Server)

| Category | Routes |
|----------|--------|
| Auth | `POST /api/auth/login`, `POST /api/auth/logout`, `GET /api/auth/session`, `POST /api/auth/dev-login` |
| User | `GET /api/user` |
| Conversations | `GET/POST /api/conversations`, `GET /api/conversations/[id]/messages`, `POST /api/conversations/[id]/messages/stream` |
| Connections | `PUT/DELETE /api/connections`, `POST /api/connections/callrail`, `GET /api/connections/status/[lawFirmId]` |
| Other | `POST /api/send-upgrade-request`, `POST /api/chat` (deprecated) |

---

## Key File Structure

```
src/
  app/
    page.tsx                    — Home/chat page
    settings/page.tsx           — Settings page
    globals.css                 — Global styles & animations
    api/auth/                   — Auth routes
    api/conversations/          — Conversation CRUD + streaming
    api/connections/            — Service connections
  components/
    chat-surface.tsx            — Main chat UI (916 lines)
    sidebar.tsx                 — Navigation sidebar (388 lines)
    icons.tsx                   — SVG icon components
    settings/                   — Settings panel components
    ui/                         — Reusable UI primitives
  lib/
    auth.ts, api.ts, useUserData.ts, types.ts, utils.ts
```

---

## State & Caching
- `sessionStorage`: user profile, conversations list
- `localStorage`: service connection credentials
- `cookie`: `justice_session` (HTTP-only)
- `URL`: `conversation_id`, `sso_token`

---

## Styling
- Custom animations: slideIn, bounceIn, fadeIn/fadeOut, modalSlideIn/modalSlideOut
- Color palette: deep purples (#2c1c6f, #250d53), light purples (#ede8f5, #f8f7fa)
- Container queries via `@container`

---

## Environment Variables
- `CHAT_API_URL` / `NEXT_PUBLIC_CHAT_API_URL` — Backend chat endpoint
- `NEXT_PUBLIC_API_URL_BASE` — Base URL for REST API
- `SENDGRID_API_KEY`, `SENDGRID_FROM_EMAIL`, `SENDGRID_TO_EMAIL`
- `DEV_AUTH_URL`, `DEV_AUTH_EMAIL`, `DEV_AUTH_PASSWORD`

## Related Files
- [[justice-sync]] — FastAPI backend
