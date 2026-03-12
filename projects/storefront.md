# Storefront — Rails Application

> Main Lawbrokr web application for law firm marketing, funnels, and lead management.

## Tech Stack
- Ruby on Rails
- Hotwire / Turbo Drive (SPA-style navigation)
- Tailwind CSS (`app/assets/stylesheets/application.tailwind.css`)
- Sidekiq (background jobs)
- PostgreSQL
- Pundit (authorization)

## Architecture Notes

### Controllers
- Controllers are NOT in subfolders — doing so would break Rails routes
- `/controller/fronts/` — all pages for non-authenticated (external) users
  - `base_controller.rb` — `def set_lead` sets up unauthenticated user leads
  - `leads_controller.rb` — saves data whenever user clicks save

### Authentication
- `authorize_controller.rb` — Pundit-based auth
- Policy classes in `/app/policies/` (e.g., `CampaignPolicy`)

### Models
- **Single Table Inheritance**: `page.rb` is the base model; everything in `/page_type/` inherits from it
- `lead_filter.rb` — lead filtering logic
- `storefront_design.rb` — global default theming model
- `Response Page` — email-type list of all responses

### Key Paths
- `app/javascript/` — custom form picker code
- `app/views/settings/../../../lead_dockets/_form.html.erb` — Hotwire example → `leaddocket_controller.js`
- `app/views/layouts/` — all main layout files
- `/storefront/lib/bot_checker.rb` — active bot detection

### Renamed Concepts
| Old Name | New Name (UI) | Code Name |
|----------|--------------|-----------|
| Campaigns | Funnel | Still `Campaign` in code |
| Journey | Workflow | Still `Journey` in code |

### Development Tips
- Modify views/controllers → refresh shows changes
- Modify config/models → may need to restart Rails
- `.blank?` — checks if nil, empty string, or empty array
- `.present?` — opposite of `.blank?`

## Related Files
- [[rails-commands]] — how to run locally
- [[sidekiq]] — background job config
- [[bot-detection]] — bot detection research
