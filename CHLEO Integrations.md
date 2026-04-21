
I was speaking to ChatGPT about a Lawbrokr --> Clio Manage Integration, where Lawbrokr would collect data on a per matter basis to understand total billed amount per contact record.

It would only be based on a lead that came through Lawbrokr. The integration has to scrape Clio Manage to find all matters associated with that contact record (if at all), and then pull data based on the total amount billed.

The goal of this integration is to provide our clients with an in-depth analysis of total revenue attributed through Lawbrokr leads.

Can you help me build that integration based on the specific end points in Clio where we "match contact record" (name, email, phone number) from Lawbrokr, search for that record in Clio Manage account, and then pull total billed revenue?

Here's Clio API docs to support the build: [https://docs.developers.clio.com/clio-manage/api-reference/](https://docs.developers.clio.com/clio-manage/api-reference/)

Show more

9:18 AM

Fetched: Clio API V4 Documentation | Clio Developer Documentation

Fetched: Clio API V4 Documentation | Clio Developer Documentation

Good — I have a solid picture of the Clio API. Let me now build the full integration spec and code. The key endpoints I'll use are:

1. **`GET /contacts.json`** — search by name/email/phone to find the Clio contact
2. **`GET /matters.json`** — filter by `client_id` to get all matters for that contact
3. **`GET /bills.json`** — filter by `matter_id` to pull billed amounts, then sum `total`


```
# Step 1: Search contact by email
curl -X GET \
  "https://app.clio.com/api/v4/contacts.json?query=jane@example.com&fields=id,name,primary_email_address,primary_phone_number&limit=10" \
  -H "Authorization: Bearer $CLIO_ACCESS_TOKEN" \
  -H "X-API-VERSION: 4.0.13"

# Step 2: Get matters for contact (replace {contact_id})
curl -X GET \
  "https://app.clio.com/api/v4/matters.json?client_id={contact_id}&fields=id,display_number,description,status,open_date&limit=200" \
  -H "Authorization: Bearer $CLIO_ACCESS_TOKEN" \
  -H "X-API-VERSION: 4.0.13"

# Step 3: Get bills for a matter (replace {matter_id})
curl -X GET \
  "https://app.clio.com/api/v4/bills.json?matter_id={matter_id}&fields=id,state,total,paid,issued_at&limit=200" \
  -H "Authorization: Bearer $CLIO_ACCESS_TOKEN" \
  -H "X-API-VERSION: 4.0.13"
```

Viewed a file, created a file, ran a command

Viewed a file, created a file, ran a command

No worries — let me build this as a React artifact with the full integration spec, endpoint map, and ready-to-copy code instead.

V

visualize

V

visualize show_widget

Here's the full integration laid out across the three Clio API calls you need. A few things worth knowing as you build this out:

**The matching logic** is the most critical part. Clio's `query` param on `/contacts.json` does a wildcard search — it's not a strict filter. So the code searches by email first, then does a client-side exact match on `primary_email_address`. That's your most reliable signal. Phone is the fallback (normalized to digits to handle formatting differences like `+1-416` vs `(416)`). Name-only is a last resort since multiple contacts could share a name.

**The `client_id` filter on matters** is exact — it returns only matters where that contact is the primary client. It will _not_ return matters where they appear as a related contact or co-counsel, so the scope is intentionally tight.

**On the billing side**, the `total` field on a bill is the full invoice amount. You likely want to track both `total` (what was billed) and `paid` (what was actually collected) so your clients can see both the attributed revenue and the collected revenue. Bills in `draft` state are excluded since they haven't been issued yet.

**A few edge cases to design for before shipping:**

- A lead who converted but used a different email in Clio than in Lawbrokr → no match. Worth building a manual override/link in your UI
- Rate limiting: Clio allows up to 200 req/min, so for firms with many matters, parallel bill fetches could trip it — add exponential backoff
- Clio has 4 data regions (US, CA, EU, AU) — you'll need to let each firm specify theirs

The OAuth scopes needed are read-only: `contacts:read`, `matters:read`, and `billing:read`.

How should I store and display the Lawbrokr revenue attribution data per lead in a database schema?

9:37 AM

Let me think through this properly for your use case before showing you the schema.

The core tension here is: Lawbrokr owns the lead record, Clio owns the billing data, and you need a **snapshot layer** in between — because Clio data can change (bills get paid, new matters open), but you also need historical attribution that doesn't mutate retroactively.

V

visualize

V

visualize show_widget

The key architectural choice here is keeping three distinct layers rather than one flat table. Here's the reasoning behind each:

`leads` is immutable Lawbrokr data. You never update it based on Clio — it's your record of what happened on your platform, when.

`lead_clio_matches` is the attribution anchor. This is the row that says "this Lawbrokr lead became this Clio contact." The `match_method` and `match_confidence` columns are critical — they let you surface name-only matches in a review queue before they corrupt your revenue numbers. `confirmed_by` being nullable is intentional: `NULL` means the system auto-matched it, a UUID means a human verified it.

`clio_matters` and `clio_matter_revenue` are the sync layer. The important design decision here is that `clio_matter_revenue` is append-only — every sync run inserts a new row rather than updating the existing one. This gives you two things for free: a revenue growth timeline per matter (useful for showing clients how their referred leads generated value over time), and resilience against Clio data changes. If a bill gets voided in Clio next week, you still have the historical record of what was billed before.

For the display side, the "key queries" tab shows the `LATERAL` join pattern, which is the right way to grab the latest snapshot per matter without a subquery per row — it scales well when you have hundreds of leads and dozens of matters each.

One thing worth deciding early: whether `total_billed` means "all invoices ever issued" or "invoices in approved/paid state only." My recommendation is to track both — `total_billed` as all non-draft bills, and `total_paid` as the actually collected amount — and let your UI decide which number to feature depending on context.