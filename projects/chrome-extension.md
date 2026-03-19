# Chrome Extension — Lawbrokr Browser Dashboard

> Browser extension providing lead notifications, funnel stats, and quick-access dashboard for Lawbrokr users.

## Tech Stack
- React (content script UI)
- Chrome Extension APIs (Manifest V3)
- Shadow DOM (style isolation)
- Chrome Storage API (state persistence)

---

## Architecture: Modal Persistence

The extension modal must survive both full page reloads and SPA-style navigations (Rails Turbo, React Router).

### The Problem
1. **Full page navigation** — browser destroys content script; Chrome re-injects it
2. **SPA navigation** (Turbo Drive) — JS context survives but DOM container is removed when `<body>` is swapped. Extension appears broken.

The Rails app at `app.lawbrokr.ca` uses Turbo Drive (SPA navigation).

### The Fix — 3 Layers

**Layer 1: MutationObserver** (primary, framework-agnostic)
- Watches `document.body` and `document.documentElement` for `childList` changes
- When `#lawbrokr-extension-root` disappears: unmounts old React tree → runs `init()` → creates new Shadow DOM + React tree → reads state from `chrome.storage`
- Fires within one microtask of removal

**Layer 2: `turbo:load` event** (Rails-specific fallback)
- If container is missing after Turbo navigation, runs `init()`

**Layer 3: `tabs.onUpdated` + PING** (full navigations only)
- Background service worker pings content script on tab `"complete"`
- If no response, programmatically re-injects `content.js`

### Icon Click Toggle Fix
- After navigation, storage still has `VISIBLE=true` from old page
- Background tracks `tabNavTimestamps` — if tab navigated within 3s, icon click always opens (never toggles closed)

### State Persistence (chrome.storage.local)
| Key | Purpose |
|-----|---------|
| `lawbrokr_extension_active` | Extension is activated |
| `lawbrokr_overlay_visible` | Panel is showing |
| `lawbrokr_floating_button` | Floating badge enabled |
| `lawbrokr_btn_position` | Badge screen position |

### Key Files
- `src/content-entry.jsx` — init(), MutationObserver, turbo:load
- `src/components/ContentOverlay.jsx` — React UI, storage state management
- `public/background.js` — icon click, tabs.onUpdated, PING

---

## Dashboard Features

### Overview Tab
- Lead attribution, clip stats, device stats, location stats

### Funnels Tab
- Total visits, total responses, live funnels, live workflows

### Leads Tab
- Lead cards with date/time
- Accepted / pending / declined sort filters
- Unread count with green indicator
- New lead notifications
- Read more leads pagination

### UI Details
- Reusable table components with chevron links
- Thousands formatting on all numbers and charts
- Percent display on lead attribution
- Footer tab buttons with icons
- Loading card skeletons
- Pop-out option
- Gear/exit buttons, standard sizing

---

## Caching Strategy
- Cache auth on page click, clear on logout
- Cache clips/funnels data
- No caching on leads (real-time)
- Queue "all time" before other time period caching

---

## Development Notes

### Local DB connection
- Edit `/Users/chriskinsman/Documents/GitHub/storefront/.envrc` to switch to local
- Run `direnv allow`

### Naming Changes
- "Social Media" → rename in chrome
- "Source" → "Marketing Source"

### Libraries Needed
- JS library to localize UTC times in extension
- JS library for North American phone number formatting

---

## Completed Tasks
See the `x -` items: all formatting, table components, caching, notifications, lead details, source lists, etc. are done.

## Related Files
- [[storefront]] — Rails backend the extension connects to

---

Change button from "Add Response" to "Create Lead" in chrome extension

Change big button on home page from "Add New Funnel" to "Add Manual Response"

Update responses button to say "+ Add Manual Response" same text as in chrome extension

Move "Search Responses" on the same line as actions, sort by, and filters. Keep "+ Add Manual Response" button in the top right corner




