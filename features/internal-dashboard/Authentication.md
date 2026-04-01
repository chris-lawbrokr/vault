## Auth Architecture
## Why three layers?

The authentication system is split across three layers — API route handlers, middleware, and the client-side AuthProvider. Each solves a different problem, and removing any one of them would create either a security gap or a degraded user experience.

---

## `app/api/auth/*` (Route Handlers)


These are the bridge between the browser and the backend API. The browser never talks to the backend directly — it calls these Next.js routes, which:

  

- Forward credentials/tokens to the backend

- Receive the JWT and refresh token back

- Store them in **httpOnly cookies** (which JavaScript can never read — this is the security win)

  

Without these, you'd have to store tokens in `localStorage` or non-httpOnly cookies, which are vulnerable to XSS.

  

### Files

  

| Route | Purpose |

|---|---|

| `app/api/auth/login/route.ts` | Proxies login to backend, sets all auth cookies |

| `app/api/auth/refresh/route.ts` | Proxies token refresh, updates access_token cookie |

| `app/api/auth/logout/route.ts` | Notifies backend, clears all auth cookies |

  

---

  

## `middleware.ts`

  

This runs **before any page or API route loads**. It's the bouncer at the door:

  

- Not logged in? Redirected to `/login` before the page even renders

- Access token expired? Refreshes it transparently before the page loads

  

Without this, a logged-out user would briefly see the dashboard before client-side code kicked in and redirected them. It also handles the case where someone returns to a tab after the browser suspended the JS interval.

  

### Decision flow

  

1. Public path (login, auth API, static assets) → allow through

2. No session cookie → redirect to `/login`

3. Session + valid access_token → allow through

4. Session + expired access_token + refresh_token → attempt silent refresh

- Refresh succeeds → update cookies and allow through

- Refresh fails → clear all auth cookies and redirect to `/login`

5. Session + no access_token + no refresh_token → expire session

  

---

  

## `lib/auth.tsx` (AuthProvider)

  

This is the **client-side state**. It provides:

  

- `user` object so components can display "Signed in as Chris"

- `login()` and `logout()` functions for the UI

- The **background refresh interval** that keeps the session alive every 10 minutes

  

Without this, there's no way for React components to know who's logged in, trigger login/logout, or proactively refresh tokens.

  

### Background refresh

  

- Every **10 minutes**, the AuthProvider calls `/api/auth/refresh` to get a new access token before the current one expires (15 min JWT)

- If a refresh fails (network drop, server error), it **retries every 10 seconds up to 3 times**

- If the backend returns a 401 (session truly expired) or all retries are exhausted, the user is logged out

  

---

  

## How they work together

  

```

User visits /dashboard

│

▼

middleware.ts ──── Has valid token? → Yes → render page

│ │

No ▼

│ lib/auth.tsx provides

▼ user state + refresh loop

redirect to /login │

│ every 10 min calls ▼

▼ app/api/auth/refresh

login form calls (keeps session alive)

app/api/auth/login

(proxies to backend, sets cookies)

```

  

---

  

## Could you just use the API routes alone?

  

If you only had the `app/api` routes, you'd be missing:

  

- **Route protection** (middleware) — unauthenticated users could land on dashboard pages before any client code runs to redirect them

- **Client-side user state** (AuthProvider) — components wouldn't know who's logged in

- **Background token refresh** (AuthProvider) — the access token would expire after 15 minutes with no automatic renewal, and users would get logged out mid-session

  

You could technically build everything in one layer, but you'd end up with either security gaps or a worse user experience.