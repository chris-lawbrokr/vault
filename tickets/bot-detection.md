# Bot Detection Research

> ClickUp: https://app.clickup.com/t/86dz4bq3u
> Date: 2026-01-27

## Goal
Research and implement bot detection for lead capture forms on Lawbrokr storefronts.

## Current Implementation
- **Ahoy** is the current analytics/tracking system
- Bot IP list: `/storefront/lib/bot_checker.rb` → `/storefront/lib/data/bot_ips.csv`

## Research Findings
- AbuseIPDB — good for IP-based detection
- JavaScript-based detection may be best long-term (for new frontend)

## Implementation Plan

### 1. Extend Ahoy
- Config: `/config/initializers/ahoy.rb`
- Start with migration to `ahoy_visits` table (in schema file)
- Add `bot_probability` field to ahoy DB table
- Extend ahoy to async handle probability check

### 2. Schema Changes
```sql
t.string  "bot_type"
t.integer "bot_confidence"
```

```bash
bin/rails db:migrate
bin/rails console
Ahoy::Visit.column_names  # verify
```

### 3. Key Paths
- `/controller/fronts/` — all external (non-authenticated) user pages
- `/controller/fronts/base_controller.rb` → `def set_lead` — where unauthenticated leads are created
- `/controller/fronts/leads_controller.rb` — saves data on user click

### 4. Notes
- Some bot flags should be overridden if form is completely filled out
- Add confidence score on initial event
- Honeypot fields (additional detection layer)
- Consider fingerprint.com for async bot scoring

### TODOs
- [ ] Remove Ahoy cutoff to fix duplicate ID issue
- [ ] Add confidence score on initial event
- [ ] Update schema.rb

## Environment Variable
```bash
export HTTP_FLY_CLIENT_IP=5f27fff5007ed38e36523f9555d29125847054979ea0355e1544536edbba14abbd8b6e755d189501
```
