Initial notes

```
browsers will block their fimngerprint cdn 

https://github.com/fingerprintjs/fingerprintjs
embed this via npm so that it is bundles in the javascript file

[4:33 PM]npm install @fingerprintjs/fingerprintjs

Embedding it directly into our generated minified code, the same way we bundle in posthog directly

The premium one has a way of "ranking" a bot, using the free one we want some way of scoring/knowing if a session is likely a bot, we don't actually care about the fingerprinting as that's already done by posthog anyways.

Can try creating a basic Node.js/Typescript API to test having a bot flagged then have the backend API mark it in posthog as a bot
```

Run it yourself

Everything below runs from the repo root

```bash
cd ~/Documents/GitHub/boxii-js
```

Terminal 1 — build + HTTPS harness

```bash
npm run integration
```

Wait for Caddy to report it's serving. First time only, in
any terminal: caddy trust (one password prompt).

Terminal 2 — the relay API

```bash
POSTHOG_PROJECT_KEY=[key here] npm run bot-api
```

Expect exactly this line

```bash
bot-api listening on http://localhost:8787 — forwarding to PostHog
```

If it says DRY RUN (no POSTHOG_PROJECT_KEY), the env var didn't take — it'll score requests but forward nothing to PostHog.

Terminal 3 — confirm both are alive before testing anything

```bash
curl http://localhost:8787/health → {"ok":true,"posthogConfigured":true}
```

Use curl without -s here. With -s, a dead server returns a silent empty line instead of an error — that's what bit you last time.

Then drive a browser

```bash
node test/integration/bot-check.mjs # headed
```

```bash
node test/integration/bot-check.mjs --headless  # strongest
```

```bash
node test/integration/bot-check.mjs --stealth   # webdriver
```

Or hit the API directly

```bash
curl -X POST http://localhost:8787/bot-signal \
	-H 'Content-Type: application/json' \
	-H 'User-Agent: HeadlessChrome/149.0.0.0' \
	-d '{"distinctId":"manual-1","customerId":"mycroft","verdict":{"is_bot":false,"bot_score":0,"bot_kind":"none","bot_signals":[]}}'
```

What success looks like

```bash
posthog /e/ POSTs: 0  (0 = posthog-js refused to send)
relay POSTs:       1
server verdict:    score=100 combined=100
signals=["ua_automation_token","no_accept_language"]
posthog (server):  {"ok":true,"status":200}
```

Or hit the API directly

```bash
curl -X POST http://localhost:8787/bot-signal \
	-H 'Content-Type: application/json' \
	-H 'User-Agent: HeadlessChrome/149.0.0.0' \
	-d '{"distinctId":"manual-1","customerId":"mycroft","verdict":{"is_bot":false,"bot_score":0,"bot_kind":"none","bot_signals":[]}}'
```

server verdict
score=100 combined=100 signals=["ua_automation_token","no_accept_language"]
posthog (server):  {"ok":true,"status":200}

---

```bash
curl -X POST http://localhost:8787/bot-signal \
  -H 'Content-Type: application/json' \
  -H 'User-Agent: HeadlessChrome/149.0.0.0' \
  -d '{"distinctId":"manual-1","customerId":"mycroft","verdict":{"is_bot":false,"bot_score":0,"bot_kind":"none","bot_signals":[]}}' | jq
```
