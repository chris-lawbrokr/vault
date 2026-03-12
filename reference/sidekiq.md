# Sidekiq — Background Jobs

> Sidekiq runs background tasks inside the Rails storefront app.

## Configuration
- `config/initializers/sidekiq.rb`
- `config/initializers/sidekiq_cron.rb`

## Job Files
- `app/sidekiq/`

## Important Warning
Renaming a cron job will create a **duplicate** entry in Redis. Always reset Redis before AND after changing cron job names.
