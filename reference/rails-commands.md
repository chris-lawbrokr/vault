# Rails Commands — Storefront

## Start Dev Server

```bash
bin/dev
```

```bash
direnv allow
```

## Access Local App

```
https://app.lvh.me:3443/
```

## Local Gem Fix
Add to Gemfile if needed:
```ruby
gem "nio4r", ">= 2.7.0"
```

## Local Test Accounts
See `credentials/secrets.md` for passwords.

Seeded from `/storefront/db/seeds.rb`:
- admin@example.com
- lawyer@example.com

## Useful Rails Console Commands
```bash
bin/rails console
bin/rails db:migrate
Ahoy::Visit.column_names    # verify schema changes
```

## Chrome Extension API (local testing)

### Login
```bash
curl -v -X POST "https://app.lvh.me:3443/api/chrome/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"YOUR_EMAIL","password":"YOUR_PASSWORD"}' \
  -c cookies.txt
```

### Create Lead
```bash
curl -v -X POST "https://app.lvh.me:3443/api/chrome/leads" \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{"first_name": "Test", "last_name": "User", "email": "newtest@lawbrokr.com", "phone": "9999999999"}'
```

### Unread Count
```bash
# Last 5 minutes
curl -v "https://app.lvh.me:3443/api/chrome/leads/unread_count?last_reset_epoch=$(($(date +%s)-300))&limit=100" \
  -b cookies.txt

# With specific epoch
curl -v "https://app.lvh.me:3443/api/chrome/leads/unread_count?last_reset_epoch=1768923038&limit=100" \
  -b cookies.txt

# No time filter
curl -v "https://app.lvh.me:3443/api/chrome/leads/unread_count" \
  -b cookies.txt
```
