# PostgreSQL Commands — Staging DB

> Connection credentials are in `credentials/secrets.md`.

## Common Commands

Replace `PASSWORD_HERE` with the actual password from credentials.

```bash
# Test connection
PGPASSWORD='PASSWORD_HERE' psql -h 35.225.236.215 -p 5432 -U postgres -d storefront_staging -c "SELECT version();"

# List schemas
PGPASSWORD='PASSWORD_HERE' psql -h 35.225.236.215 -p 5432 -U postgres -d storefront_staging -c "\dn"

# List tables
PGPASSWORD='PASSWORD_HERE' psql -h 35.225.236.215 -p 5432 -U postgres -d storefront_staging -c "\dt"

# Justice schema tables
PGPASSWORD='PASSWORD_HERE' psql -h 35.225.236.215 -p 5432 -U postgres -d storefront_staging -c "\dt justice.*"

# Query justice users
PGPASSWORD='PASSWORD_HERE' psql -h 35.225.236.215 -p 5432 -U postgres -d storefront_staging -c "SELECT * FROM justice.users LIMIT 1;"

# Dump schema only
pg_dump -s -h 35.225.236.215 -p 5432 -U postgres storefront_staging
```

## Justice Sync — Register User (local)
```bash
curl -X POST http://127.0.0.1:8000/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"YOUR_EMAIL","password":"YOUR_PASSWORD"}'
```
